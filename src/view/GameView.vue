<script lang="ts" setup>
import { ref, computed, onMounted, CSSProperties } from "vue";

import { GameConfig, GameStatus } from "../type/game-types";

const gameStatus = ref<string>(GameStatus.WAITING);
const timeID = ref<number>(0);
const score = ref<number>(0);
/**
 * NOTE: tree的初始数据
 */
const TREE_INIT_LOCATION: number = 450;
const tree = ref<HTMLDivElement>();
const treeTranslate = ref<number>(0);

/**
 * NOTE: dinosaur的初始数据
 */
const isJump = ref<boolean>(false);
const dinosaurTranslate = ref<number>(0);

const treeStyle = computed(() => {
  return <CSSProperties>{
    left: `${TREE_INIT_LOCATION}px`,
    transform: `translateX(${treeTranslate.value}px)`,
  };
});

const dinosaurStyle = computed(() => {
  return <CSSProperties>{
    transform: `translateY(${dinosaurTranslate.value}px)`,
  };
});

/**
 * NOTE:首先判断游戏是否开始
 * 1. 不允许主角二段跳
 */
function dinosaurJump() {
  if (gameStatus.value === GameStatus.RUNNING) {
    let timeId
    if (isJump.value) {
      return null;
    }
    isJump.value = true;
    dinosaurTranslate.value = -150;
    setTimeout(() => {
      dinosaurTranslate.value = 0;
      isJump.value = false;
    },1000);
  }
}

function updateGameStatus() {
  if ((gameStatus.value = GameStatus.RUNNING)) {
    if (treeTranslate.value < -450) {
      treeTranslate.value = 0;
    }
    score.value += 1;
    treeTranslate.value -= GameConfig.TREE_VELOCITY;
  }
}

function gameStart() {
  clearInterval(timeID.value);
  gameStatus.value = GameStatus.RUNNING;
  timeID.value = setInterval(() => {
    updateGameStatus();
  }, 100);
}

onMounted(() => {
  window.addEventListener("keyup",dinosaurJump)
});
</script>

<template>
  <div id="wrapper">
    <button @click="gameStart">开始游戏</button>
    <div>
      <span>分数{{ score }}</span>
    </div>
    <div id="dinosaur" :style="dinosaurStyle" @click="dinosaurJump"></div>
    <div id="tree" ref="tree" :style="treeStyle"></div>
    <div id="cloud"></div>
  </div>
</template>

<style scoped>
#wrapper {
  width: 800px;
  height: 500px;
  border: 2px solid black;
  position: relative;
  overflow: hidden;
}

#dinosaur {
  width: 50px;
  height: 50px;
  background-color: black;
  position: absolute;
  bottom: 0px;
  left: 50px;
  transition: 1s ease-in-out;
}
#tree {
  width: 30px;
  height: 100px;
  position: absolute;
  background-color: green;
  bottom: 0px;
}
</style>
