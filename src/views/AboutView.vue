<template>
  <div class="container" ref="containerRef" @scroll="scroll">
    <div class="placeholder" :style="{ height: placeHeight + 'px' }">
      <div class="item" v-for="(item, index) in arrTemp" :key="item" :style="{position: 'absolute',top: `${itemHeight * item}px`}">
        <!-- 注意此处的item，因为本身数据mock时，自己就代表着自己在数组中的索引，所以直接用本身item。但是基本上都需要使用startIndex，循环到endIndex，获得自己在数组中的索引，react的本身语法jsx
         很容易实现，vue模版语法，除非在元素中添加对应索引实现-->
        {{ item }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, computed, onMounted, h } from "vue";
const arr = [];
for (let i = 0; i < 1000; i++) {
  arr.push(i);
}
const containerRef = ref();
const startIndex = ref(0); // 开始索引
const endIndex = ref(0);  // 结束索引

const showLimit = ref(0); // 可视区能展示最大个数
const itemHeight = 100; // 子元素高度
const containerHeight = ref(0); //容器高度，本身固定
const overNum = 6;  // 总缓冲长度
const halfOverNum = Math.ceil(overNum / 2);



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

// 滚动时计算startIndex,endIndex,以及offset
// 计算属性获得展示数组
const scroll = () => {
  const scrollTop = containerRef.value.scrollTop;
  startIndex.value =Math.floor(scrollTop / itemHeight)
  endIndex.value = Math.min(arr.length, startIndex.value + showLimit.value+ halfOverNum+ 1); //下缓冲区索引
  startIndex.value = Math.max(0, Math.floor(scrollTop / itemHeight)- halfOverNum) // 上缓冲区索引，注意放在获取下缓冲区后面
  // for(let i= startIndex.value; i<=endIndex.value, i++){
        // arr[i].index= i
  // }
};


// // 容器下压元素到可视区方法
// const scroll = () => {
//   const scrollTop = containerRef.value.scrollTop;
//   startIndex.value = Math.floor(scrollTop / itemHeight);
//   endIndex.value = Math.min(arr.length, startIndex.value + showLimit.value + halfOverNum + 1); // 直接，拉到下缓冲区位置，同时不超过数组本身最大长度。数组slice不包含右边，所以+1

//   paddingTop.value = itemHeight * Math.max(startIndex.value - halfOverNum, 0); // 注意此处将下压时，只压item高度，多余的不足的由浏览器控制。同时留出上缓冲区位置不压。
//   if (startIndex.value <= halfOverNum) {
//     // 最开始往下滑时，缓冲区内就全部获取到，不切片
//     startIndex.value = 0;
//   } else if (startIndex.value > arr.length - showLimit.value - halfOverNum) {
//     // 滑到最下面时，为保证上缓冲区数量，必须保证startIndex的位置至少小于等于缓冲区的所有
//     startIndex.value = arr.length - showLimit.value - halfOverNum;
//   } else {
//     // 中间滑动时，就只减少缓冲区的长度。
//     startIndex.value -= halfOverNum;
//   }
// };
</script>

<style>
.container {
  width: 500px;
  height: 500px;
  overflow: auto;
  background-color: skyblue;
}


.item {
  /* display: flex;
  align-items: center;
  justify-content: center; */
  height: 100px;
  border-bottom: 1px solid gray;
}

.placeholder {
  position: relative;
}
</style>
