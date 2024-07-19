<template>
  <div class="about" ref="containerRef" @scroll="scroll">
    <div class="placeholder" :style="{ height: placeHeight + 'px' }"></div>
    <div :style="{ paddingTop: paddingTop + 'px' }">
      <div class="item" v-for="item in arrTemp" :key="item">
        {{ item }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, computed, onMounted } from "vue";
const arr = [];
for (let i = 0; i < 1000; i++) {
  arr.push(i);
}
const containerRef = ref();
const startIndex = ref(0);
const endIndex = ref(0);
const showLimit = ref(0); // 可视区能展示最大个数
const itemHeight = 100; // 子元素高度
const containerHeight = ref(0); //容器高度，本身固定
const overNum = 6;
onMounted(() => {
  // 挂载后才能知道容器高度，计算endIndex
  containerHeight.value = containerRef.value.clientHeight;
  showLimit.value = Math.ceil(containerHeight.value / itemHeight);
  endIndex.value = showLimit.value;
});
const placeHeight = computed(() => {
  // 填充高度，滚动条显示
  return itemHeight * arr.length;
});
const arrTemp = computed(() => {
  //展示数组
  return arr.slice(startIndex.value, endIndex.value);
});
const paddingTop = ref(0);
// 滚动时计算startIndex,endIndex,以及offset
// 计算属性获得展示数组
// const scroll = () => {
//   const scrollTop = containerRef.value.scrollTop;
//   startIndex.value = Math.min(
//     Math.floor(scrollTop / itemHeight),
//     arr.length - showLimit.value - 3
//   );

//   endIndex.value = Math.min(arr.length, startIndex.value + showLimit.value + 6);
//   paddingTop.value = itemHeight * (startIndex.value - 3); // 注意此处将下压时，只压item高度，多余的不足的由浏览器控制，
// };

const scroll = () => {
  const scrollTop = containerRef.value.scrollTop;
  startIndex.value = Math.floor(scrollTop / itemHeight);
  endIndex.value = Math.min(arr.length, startIndex.value + showLimit.value + 3); //注意顺序，确保了前3个滑动时，下滑个数仍然为缓冲个数

  paddingTop.value = itemHeight * Math.max(startIndex.value - 3, 0); // 注意此处将下压时，只压item高度，多余的不足的由浏览器控制，
  if (startIndex.value <= 3) {
    startIndex.value = 0;
  } else if (startIndex.value > arr.length - showLimit.value - 3) {
    startIndex.value = arr.length - showLimit.value - 3;
  } else {
    startIndex.value -= 3;
  }
  // console.log(scrollTop, startIndex.value, endIndex.value, paddingTop.value);
};
</script>

<style>
.about {
  position: relative;
  width: 500px;
  height: 500px;
  overflow: auto;
  background-color: skyblue;
}

.placeholder {
  position: absolute;
  left: 0;
  top: 0;
  right: 0;
  z-index: -1;
}
.item {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100px;
  border-bottom: 1px solid gray;
}
</style>
