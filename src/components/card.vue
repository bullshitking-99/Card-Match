<script setup>
import { ref } from "vue";

// 传入属性 cardInfo 和 cardControl
const props = defineProps({
  cardInfo: String, // num kind index
  cardControl: String, // front back disappear
});

// 抽离数据
let [info, index] = props.cardInfo.split("-");
index = Number(index);
const num = info % 10;
const kind = Math.floor(info / 10);

// 点击事件 - 向上传递卡牌信息 - num 和 index
const emit = defineEmits(["clickCard"]);
function clickHandler() {
  emit("clickCard", { num, index });
}
</script>

<template>
  <div
    class="card"
    :class="[
      { front: cardControl === 'front' },
      { back: cardControl === 'back' },
      { disappear: cardControl === 'disappear' },
    ]"
    @click="clickHandler"
  >
    <h1
      v-if="cardControl === 'front' || cardControl === 'disappear'"
      :class="kind === 1 ? 'redPeach' : 'blackPeach'"
    >
      {{ num }}
    </h1>
  </div>
</template>

<style scoped>
/* card基础样式 */

.card {
  width: 10vw;
  min-width: 95px;
  height: 18vh;
  min-height: 141px;
  border-radius: 5%;
  background-color: whitesmoke;

  user-select: none;
  display: flex;
  justify-content: center;
  align-items: center;
}

.redPeach {
  color: red;
}
.blackPeach {
  color: black;
}

/* card的三种状态 front back disappear */

.front {
  transition: all 0.5s ease;
  font-size: 40px;
}

.back {
  position: relative;
  /* hover退出时保持平滑 */
  transition: all ease 0.3s;
}
/* 防止背景图片transition */
.back::after {
  content: "";
  width: 100%;
  height: 100%;
  position: absolute;
  background-image: url(/favicon.ico);
  background-repeat: no-repeat;
  background-size: 50%;
  background-position: 50%;
}
.back:hover {
  transition: all ease 0.3s;
  transform: translateY(-5px);
  box-shadow: 1px 2px 15px gray;
}

.disappear {
  /* 动画结束时自动隐藏，不再响应事件 */
  transition: all ease 0.5s;
  /* transition-delay: 1s; */
  opacity: 0;
  transform: scale(0);
  visibility: hidden;
}

/* 卡片状态过渡 - 翻转 */
/* 进入front时 从左到右翻转 */
/* .overturn_backToFront {
  animation: backToFront 0.5s ease-in-out;
}
@keyframes backToFront {
  0% {
    transform: rotateY(0deg);
  }
  100% {
    transform: rotateY(90deg);
  }
} */
/* 从front 到 back时，从右向左翻转 */

/* .overturn_frontToBack {
  animation: frontToBack 0.5s ease-in-out;
}
@keyframes frontToBack {
  0% {
    transform: rotateY(0deg);
  }
  100% {
    transform: rotateY(90deg);
  } 
}*/
</style>
