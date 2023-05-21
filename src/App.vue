<template>
  <div class="main">
    <div class="count">
      <h2 class="stroke">{{ count }}</h2>
      <div class="progress-bar" v-show="isStartCalculate" :style="{
        width: progressBarWidth
      }"></div>
    </div>
    <p>
      <span>{{ beatNum }} 拍子</span><span v-if="isStartCalculate">，速度 {{ String(60 / (avgTime * 0.001)).split(".")[0] }}
      </span>
    </p>
    <div class="btn-group">
      <button type="button" class="start-btn" :disabled="isStartCalculate" @click="onClickstartButton">
        🥁
      </button>
      <button type="button" class="check-btn" :disabled="isStartCalculate" @click="onClickCheckButton">
        ✔
      </button>
    </div>
  </div>
  <div class="description">
    <p>使用方法：</p>
    <p>1. 随着节奏一下一下的点击 🥁 按钮直到最后一拍</p>
    <p>2. 在最后一拍的时候点击 ✔ 按钮，开始数拍子</p>
    <p>例如，你想要数一个速度为 100 且是 4 拍子的歌。先按照速度点击左边的 🥁 按钮 3 下，最后点击 ✔ 按钮 1 下开始数拍子。</p>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue';

const count = ref(0); // 计数
const beatNum = ref(0); // 拍子数
const avgTime = ref(0); // 平均间隔
const isStartCalculate = ref(false); // 是否开始计算
const timeLog = ref([]); // 记录每次点击的时间
const currentBeat = ref(1); // 当前在第几拍

const progressBarWidth = computed(() => {
  return `${(currentBeat.value / beatNum.value) * 100}%`;
});

function caculateTimeInterval(arr) {
  let res = [];
  for (let i = 0; i < arr.length; i++) {
    if (i > 0) {
      res.push(arr[i] - arr[i - 1]);
    }
  }
  return res;
}

function onClickstartButton() {
  timeLog.value.push(new Date().getTime());
  beatNum.value++;
  count.value = 1;
}

function onClickCheckButton() {
  timeLog.value.push(new Date().getTime());
  beatNum.value++;
  console.log('拍子', beatNum.value);
  const times = caculateTimeInterval(timeLog.value);
  avgTime.value = times.reduce((a, b) => Number(a) + Number(b)) / times.length;
  console.log('平均间隔', avgTime.value);
  // 开始计算
  setTimeout(() => {
    // 第一拍
    count.value++;
    isStartCalculate.value = true;
    setInterval(() => {
      // 其他拍
      if (currentBeat.value >= beatNum.value) {
        currentBeat.value = 1;
        count.value++;
      } else {
        currentBeat.value++;
      }
      // console.log('当前拍子', currentBeat.value);
    }, avgTime.value);
  }, avgTime.value);

}
</script>

<style scoped>
h2 {
  font-size: 7rem;
  margin: 1rem;
  --font-color: #213547;
  --stroke-color: #fff;
}

.main p {
  margin-bottom: 1rem;
}

.description {
  margin-top: 2rem;
}

.description p {
  margin: 0.25rem 0;
  font-size: 0.75rem;
}

.check-btn {
  margin-left: 2rem;
}

.count {
  position: relative;
}

.progress-bar {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: -1;
  width: 0;
  background-color: #409eff;
}
</style>
