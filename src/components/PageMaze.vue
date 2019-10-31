<template>
  <div class="page-maze-container" :style="{backgroundPosition: computeBackgroundPosition}">
    <div class="page-maze" :style="{transform: computeTranslate}">
      <h1>我是誰</h1>
      <Page
        v-for="(item, index) in maze"
        :key="index"
        :pageInfo="item"
        :id="index"
        :currentCoords="currentCoords"
        :colorScheme="colorScheme"
      />
    </div>
    <div class="page-maze-controller">
      <div class="page-maze-controller-group">
        <button
          :class="{
            'page-maze-controller-button': true,
            'page-maze-controller-button--disabled': !hasNeighbor('up')
          }"
          @click="handleControllerClick('up')"
        >↑</button>
      </div>
      <div class="page-maze-controller-group page-maze-controller-group--middle">
        <button
          :class="{
            'page-maze-controller-button': true,
            'page-maze-controller-button--disabled': !hasNeighbor('left')
          }"
          @click="handleControllerClick('left')"
        >←</button>
        <button
          :class="{
            'page-maze-controller-button': true,
            'page-maze-controller-button--disabled': !hasNeighbor('right')
          }"
          @click="handleControllerClick('right')"
        >→</button>
      </div>
      <div class="page-maze-controller-group">
        <button
          :class="{
            'page-maze-controller-button': true,
            'page-maze-controller-button--disabled': !hasNeighbor('down')
          }"
          @click="handleControllerClick('down')"
        >↓</button>
      </div>
    </div>
  </div>
</template>

<script>
import Page from "./Page";

export default {
  name: "PageMaze",
  components: {
    Page
  },
  props: {
    maze: {
      type: Object,
      require: true
    },
    mazeIndexTable: {
      type: Array,
      require: true
    },
    colorScheme: {
      type: Array,
    }
  },
  data() {
    return {
      currentCoords: {
        x: 0,
        y: 0
      },
      backgroundSmooth: 5
    };
  },
  computed: {
    computeBackgroundPosition() {
      return (
        50 +
        this.currentCoords.x * this.backgroundSmooth +
        "% " +
        (50 + this.currentCoords.y * this.backgroundSmooth) +
        "%"
      );
    },
    computeTranslate() {
      return (
        "translate(" +
        this.currentCoords.x * -100 +
        "%, " +
        this.currentCoords.y * -100 +
        "vh)"
      );
    }
  },
  methods: {
    handleControllerClick(dir) {
      switch (dir) {
        case "up":
          this.currentCoords.y--;
          break;
        case "down":
          this.currentCoords.y++;
          break;
        case "left":
          this.currentCoords.x--;
          break;
        case "right":
          this.currentCoords.x++;
          break;
        default:
          break;
      }
    },
    hasNeighbor(dir) {
      const tableY = this.mazeIndexTable.map(e => e[1]);
      const tableX = this.mazeIndexTable.map(e => e[0]);
      switch (dir) {
        case "up":
          return (
            this.mazeIndexTable.filter(e => {
              return (
                e[0] === this.currentCoords.x &&
                e[1] === this.currentCoords.y - 1
              );
            }).length > 0
          );
          break;
        case "down":
          return (
            this.mazeIndexTable.filter(e => {
              return (
                e[0] === this.currentCoords.x &&
                e[1] === this.currentCoords.y + 1
              );
            }).length > 0
          );
          break;
        case "left":
          return (
            this.mazeIndexTable.filter(e => {
              return (
                e[1] === this.currentCoords.y &&
                e[0] === this.currentCoords.x - 1
              );
            }).length > 0
          );
          break;
        case "right":
          return (
            this.mazeIndexTable.filter(e => {
              return (
                e[1] === this.currentCoords.y &&
                e[0] === this.currentCoords.x + 1
              );
            }).length > 0
          );
          break;
        default:
          break;
      }
    }
  }
};
</script>

<style lang="scss" scoped>
.page-maze-container {
  position: relative;
  overflow: hidden;
  width: 100%;
  height: 100vh;
  background-image: url("../assets/bg.jpg");
  transition: 0.666s ease-in-out;
  .page-maze {
    position: relative;
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    color: #ffffff;
    transition: 0.666s ease-in-out;
  }
  .page-maze-controller {
    position: fixed;
    right: 0;
    bottom: 0;
    width: 120px;
    height: 120px;
    margin: 10px;
    .page-maze-controller-group {
      position: relative;
      width: 120px;
      height: 40px;
      display: flex;
      justify-content: space-around;
      align-items: center;
    }
    .page-maze-controller-group--middle {
      justify-content: space-between;
    }
    .page-maze-controller-button {
      width: 40px;
      height: 40px;
      border: none;
      border-radius: 50%;
      background-color: #ffffff;
    }
    .page-maze-controller-button--disabled {
      pointer-events: none;
      color: #898989;
      opacity: 0.2;
    }
  }
}
</style>