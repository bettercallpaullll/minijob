<template>
  <div id="minijob">
    <div class="header">
      <!-- hier das font-awesome icon -->
      <p class="close-btn" @click="close">X</p>
      <img alt="header-image" class="header-image" :src="headerImage()" />
      <div class="name">
        <img alt="logo" class="logo" :src="logoImage()" />
        <p>{{ name }}</p>
      </div>
      <div>
        <div v-if="nextLevel == null" class="max-level">
          Maximales Level erreicht: {{ currentLevel.tier }} mit {{ exp }}XP
        </div>
        <div v-else class="player-data">
          <p>Level {{ currentLevel.tier }}</p>
          <div class="progress xp-progress">
            <div
              class="progress-bar"
              role="progressbar"
              :style="{ width: getProgress() }"
              aria-valuenow="25"
              aria-valuemin="0"
              aria-valuemax="100"
            >
              {{ exp }}XP
            </div>
          </div>
          <p>Level {{ nextLevel.tier }}</p>
        </div>
      </div>
    </div>
    <div class="main">
      <ul class="levels" v-if="selectedLevel == null">
        <li
          v-for="(level, index) in levels"
          :key="index"
          @mouseover="triggerLockEffect(true, level.unlocked, level.tier)"
          @mouseleave="triggerLockEffect(false, level.unlocked, level.tier)"
        >
          <div class="level" v-bind:class="{ levelUnlocked: level.unlocked }">
            <p
              class="level-name"
              v-bind:class="{ levelLocketImg: !level.unlocked }"
            >
              Level {{ level.tier }} - {{ level.name }}
            </p>
            <div
              class="level-content"
              v-bind:class="{ levelLocketImg: !level.unlocked }"
            >
              <div class="level-image-wrapper">
                <img
                  alt="level-image"
                  class="level-image"
                  :src="levelImage(level.tier)"
                />
              </div>
              <div class="level-data">
                <p>
                  Führerschein: Klasse
                  <span
                    v-bind:class="{
                      levelLocketColor: !level.unlocked,
                      levelUnlocketColor: level.unlocked,
                    }"
                    >{{ level.licence }}</span
                  >
                </p>
                <p>
                  Fahrzeug:
                  <span
                    v-bind:class="{
                      levelLocketColor: !level.unlocked,
                      levelUnlocketColor: level.unlocked,
                    }"
                    >{{ level.vehicle }}</span
                  >
                </p>
                <p>
                  Gehalt:
                  <span
                    v-bind:class="{
                      levelLocketColor: !level.unlocked,
                      levelUnlocketColor: level.unlocked,
                    }"
                    >{{ level.salary }}$</span
                  >
                </p>
              </div>
              <div class="accept-job">
                <img
                  @click="selectLevel(level)"
                  src="../../assets/img/accept_job.png"
                  alt=""
                  @mouseover="acceptJobHover = level.tier"
                  @mouseout="acceptJobHover = null"
                  :class="{
                    acceptJobHoverEffect:
                      acceptJobHover == level.tier && level.unlocked,
                  }"
                />
              </div>
            </div>
            <img
              class="padlock"
              v-if="!level.unlocked"
              src="../../assets/img/padlock.png"
              alt="padlock"
              :class="{
                selectedLocketLevelEffect:
                  selectedLocketLevel && level.tier == selectedLocketLevelTier,
              }"
            />
          </div>
        </li>
      </ul>
      <div v-else>
        <div class="route-header">
          <p>
            Level
            {{ selectedLevel.tier }}
            -
            {{ selectedLevel.name }}
          </p>
        </div>
        <ul class="routes">
          <li
            v-for="(route, index) in selectedLevel.routes"
            :key="index"
            class="route"
            :class="{
              routeSelected:
                selectedRoute != null && selectedRoute.length == route.length,
            }"
            @click="selectRoute(route)"
          >
            <div class="route-data">
              <p>
                {{ getRouteLength(route.length) }} Strecke:
              </p>
              <p>
                Zeitangabe:
                {{ route.estimatedTime }} Minuten
              </p>
              <p>
                Gehaltbonus:
                <span class="routeBonusColor">{{ route.salaryBonus }}$</span>
              </p>
              <p>
                Erfahrungsbonus:
                <span class="levelUnlocketColor">{{ route.expBonus }}XP</span>
              </p>
            </div>
            <div class="route-image">
              <img
                alt="route-image"
                :src="routeImage(route.length)"
              />
            </div>
          </li>
        </ul>
        <div class="manage-route">
          <button class="back-wrapper">
            <img
              @click="back"
              class="back"
              src="../../assets/img/arrow_left.png"
              alt="back"
            />
            <p @click="back">ZURÜCK</p>
          </button>
          <button
            class="start-wrapper"
            @click="startJob()"
            :class="{ routeSelectedBtn: selectedRoute != null }"
          >
            <p>STARTEN</p>
            <img
              @click="startJob()"
              class="back"
              src="../../assets/img/arrow_right.png"
              alt="back"
            />
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: "",
      exp: 0,
      levels: [],
      selectedLevel: null,
      currentLevel: { tier: 0 },
      nextLevel: { tier: 0 },
      selectedLocketLevel: false,
      selectedLocketLevelTier: null,
      acceptJobHover: false,
      selectedRoute: null,
    };
  },
  methods: {
    getProgress() {
      // if data not loaded
      if (
        this.levels.length == 0 ||
        this.currentLevel == null ||
        this.nextLevel == null
      )
        return "100%";
      var diff = this.nextLevel.neededXP - this.currentLevel.neededXP;
      var onePercent = diff / 100;
      var playerPercent = this.exp - this.currentLevel.neededXP;
      var playerReached = playerPercent / onePercent;
      return `${playerReached}%`;
    },
    levelImage(tier) {
      if (this.name == "") {
        return;
      }
      return require(`../../assets/img/${this.name}/lvl${tier}.png`);
    },
    logoImage() {
      if (this.name == "") {
        return;
      }
      return require(`../../assets/img/${this.name}/logo.png`);
    },
    headerImage() {
      if (this.name == "") {
        return;
      }
      return require(`../../assets/img/${this.name}/header.png`);
    },
    routeImage(len) {
      if (this.name == "") {
        return;
      }
      return require(`../../assets/img/${this.name}/route${len}.png`);
    },
    init(data) {
      this.name = data.jobname;
      this.exp = data.playerXP;
      this.levels = data.levels;
      this.currentLevel = this.levels[0];
      this.levels.forEach((level) => {
        if (this.exp >= level.neededXP) {
          this.currentLevel = level;
          level.unlocked = true;
        } else {
          level.unlocked = false;
        }
      });
      var lastLevel = this.levels[this.levels.length - 1];
      if (lastLevel.neededXP <= this.exp) {
        this.nextLevel = null;
      } else this.currentLevel.tier < lastLevel.tier;
      this.nextLevel = this.levels.find(
        (level) => level.tier == this.currentLevel.tier + 1
      );
    },
    close() {
      this.$emit("close");
    },
    selectLevel(level) {
      if (level.unlocked) {
        this.selectedLevel = level;
        this.acceptJobHover = null;
      }
    },
    backgroundImage() {
      return {
        backgroundImage: `url(~@/assets/img/${this.name}/header.png)`,
      };
    },
    startJob() {
      if (this.selectLevel == null || this.selectRoute == null) return;
      window.alt.emit("start-job", this.selectedLevel, this.selectedRoute);
      this.close();
    },
    getRouteLength(len) {
      switch (len) {
        case 0:
          return "Kurze";
        case 1:
          return "Mittlere";
        case 2:
          return "Lange";
      }
    },
    back() {
      this.selectedLevel = null;
      this.selectedRoute = null;
    },
    selectRoute(route) {
      this.selectedRoute = route;
    },
    triggerLockEffect(toogle, unlocked, tier) {
      if (unlocked) return;
      if (toogle) {
        this.selectedLocketLevel = true;
        this.selectedLocketLevelTier = tier;
      } else {
        this.selectedLocketLevel = false;
        this.selectedLocketLevelTier = null;
      }
    },
  },

  mounted() {
    this.init({
      // folder structure: 
      // in assents/img
      // --> jobname
      //   --> header.png
      //   --> logo.png
      //   --> lvl1.png
      //   --> lvl2.png
      //   --> lvl3.png
      //   --> route0.png
      //   --> route1.png
      //   --> route2.png
      // foreach route or level increment
      // lvl start at 1, route at 0
      // first route always neededXP = 0!
      jobname: "postop",
      playerXP: 900,
      levels: [
        {
          tier: 1,
          name: "Zeitungsbote",
          licence: "-",
          vehicle: "Cruiser",
          salary: 120,
          neededXP: 0,
          routes: [
            {
              length: 1,
              salaryBonus: 20,
              estimatedTime: 2,
              expBonus: 50,
            },
            {
              length: 2,
              salaryBonus: 50,
              estimatedTime: 2,
              expBonus: 100,
            },
          ],
        },
        {
          tier: 2,
          name: "Paketzusteller",
          licence: "C",
          vehicle: "Boxville",
          salary: 245,
          neededXP: 400,
          routes: [
            {
              length: 0,
              salaryBonus: 0,
              estimatedTime: 2,
              expBonus: 0,
            },
            {
              length: 1,
              salaryBonus: 20,
              estimatedTime: 2,
              expBonus: 50,
            },
            {
              length: 2,
              salaryBonus: 50,
              estimatedTime: 2,
              expBonus: 100,
            },
          ],
        },
        {
          tier: 3,
          name: "Kraftfahrer",
          licence: "CE",
          vehicle: "Pounder",
          salary: 325,
          neededXP: 1000,
          routes: [
            {
              length: 0,
              salaryBonus: 0,
              estimatedTime: 2,
              expBonus: 0,
            },
            {
              length: 1,
              salaryBonus: 20,
              estimatedTime: 2,
              expBonus: 50,
            },
            {
              length: 2,
              salaryBonus: 50,
              estimatedTime: 2,
              expBonus: 100,
            },
          ],
        },
      ],
    });
    
    // window.alt.on("open-minijob", (data) => {
    //   this.init(data);
    // });
  },
};
</script>

<style src="./minijob.css" scoped></style>
