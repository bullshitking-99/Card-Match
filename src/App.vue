<script setup>
import { ref, watch } from "vue";
import card from "./components/card.vue";
const FRONT = "front";
const BACK = "back";
const DISAPPEAR = "disappear";

// 卡牌数据 - 红桃1-8 黑桃1-8
function createCards() {
  let cardArr = new Array(16).fill(null);
  for (let i = 0; i < cardArr.length; i++) {
    if (i <= 7) cardArr[i] = i + 1;
    if (i === 8) cardArr[i] = 11;
    if (i > 8) cardArr[i] = cardArr[i - 1] + 1;
  }
  // 洗牌
  function shuffle(arr) {
    let i, j;
    for (i = arr.length; i; i--) {
      // j为0~length-1，此处i不能为0，因为i为0则j一定为0
      j = Math.floor(Math.random() * i);
      // 交换i j
      [arr[i - 1], arr[j]] = [arr[j], arr[i - 1]];
    }
    return arr;
  }
  cardArr = shuffle(cardArr);

  cardArr = cardArr.map((num, index) => {
    return { cardInfo: num + "-" + index, cardControl: "back" };
  });

  return cardArr;
}

const cards = ref(createCards());
let restCards = ref(cards.value.length);

// 每轮只能点击两次，相同则消除，否则翻转
// 每轮内点击同一卡片无响应
function round() {
  let lastCard = {
    num: null,
    index: null,
  };

  return function compare(cardInfo) {
    const { num, index } = cardInfo;
    // 将卡牌翻面
    cards.value[index].cardControl = FRONT;

    // 保存第一张牌信息
    if (!lastCard.num) {
      lastCard.num = num;
      lastCard.index = index;
      return;
    }
    // 与上一张卡牌判断
    // 点击同一张卡牌无响应
    if (lastCard.index === index) return;
    if (lastCard.num === num) {
      // 相同卡牌，消除
      setTimeout(() => {
        cards.value[index].cardControl = DISAPPEAR;
        cards.value[lastCard.index].cardControl = DISAPPEAR;
        restCards.value -= 2;
      }, 1000);
    }
    // 不同卡牌，延时还原
    if (lastCard.num !== num) {
      setTimeout(() => {
        cards.value[index].cardControl = BACK;
        cards.value[lastCard.index].cardControl = BACK;
      }, 1000);
    }
    // 开始下一轮
    lastCard.num = null;
  };
}
// 卡牌点击事件，闭包round()()无法直接注入模板
const clickHandler = round();

// 监听牌库数量，所有牌清除则提示游戏完成
watch(restCards, () => {
  if (!restCards.value) {
    const res = confirm("恭喜你，游戏完成，是否继续一把");
    if (res) cards.value = createCards();
  }
});
</script>

<template>
  <main>
    <card
      v-for="card in cards"
      :cardInfo="card.cardInfo"
      :cardControl="card.cardControl"
      @clickCard="clickHandler"
    ></card>
  </main>
</template>

<style scoped>
main {
  background-color: bisque;
  width: 45vw;
  min-width: 470px;
  height: 80vh;
  min-height: 630px;
  margin: 10vh auto;
  border-radius: 1%;

  box-shadow: 2px 3px 20px gray;

  display: flex;
  flex-wrap: wrap;
  justify-content: space-evenly;
  align-items: center;
}
</style>
