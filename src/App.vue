<script lang="ts" setup>
import { ref } from "vue";

// 数据
interface Icard {
  content: number;
  state: "open" | "close" | "shake" | "disappear";
  color: "gray" | "smoke";
}
const cards = ref<Icard[]>([]);

// 生成数据
function createCards() {
  for (let i = 0; i < 16; i++) {
    cards.value.push({ content: i + 1, state: "close", color: "gray" });
  }
  for (let i = 8; i < 16; i++) {
    cards.value[i].content = i - 7;
    cards.value[i].color = "smoke";
  }
  shuffle(cards.value);
}
createCards();

// 控制数据
// 第一次点击的卡牌
let lastCard: Icard | null;
let restCard: number = cards.value.length;

// 方法
// 洗牌：每次在未放置队列中随机抽一张，放到最后或上一张牌的前面
function shuffle(arr: Icard[]): void {
  let i: number; // 抽取队列的最后一张，也代表抽取队列的数量
  let j: number; // 随机抽出的牌
  for (i = arr.length; i > 0; i--) {
    j = Math.floor(Math.random() * i);
    // 交换 i j
    const card_i = arr[i - 1];
    const card_j = arr[j];

    Object.keys(card_i).map((p) => {
      [card_i[p], card_j[p]] = [card_j[p], card_i[p]];
    });
  }
}

function cardClickHandler(index: number): void {
  const card = cards.value[index];
  if (card === lastCard) return;

  const state = card.state;
  card.state = state === "close" ? "open" : "close";

  // 开始判断
  // lastCard === null
  //   ? (lastCard = card)
  //   : lastCard.content === card.content // 是null这一行还是会被执行
  //   ? matchRight()
  //   : matchError();

  if (!lastCard) {
    lastCard = card;
    return;
  }

  lastCard && lastCard.content === card.content ? matchRight() : matchError();

  function matchRight() {
    (lastCard as Icard).state = card.state = "disappear";
    lastCard = null;
    restCard = restCard - 2;

    if (restCard === 0) {
      setTimeout(() => {
        alert("congratulation");
        cards.value.length = 0;
        createCards();
      }, 500);
    }
  }
  function matchError() {
    (lastCard as Icard).state = card.state = "shake";
    const _lastCard = lastCard;
    lastCard = null;
    setTimeout(() => {
      (_lastCard as Icard).state = card.state = "close";
    }, 500);
  }
}
</script>

<template>
  <main>
    <div
      v-for="(card, index) in cards"
      :class="[
        { close: card.state === 'close' },
        { shake: card.state === 'shake' },
        { disappear: card.state === 'disappear' },
        [card.color === 'smoke' ? 'smoke' : 'gray'],
      ]"
      @click="cardClickHandler(index)"
    >
      {{ card.content }}
    </div>
  </main>
</template>

<style scoped lang="scss">
main {
  width: 60vw;
  height: 60vw;
  margin: 10vh auto;
  padding: 10px;

  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr 1fr 1fr;
  grid-gap: 10px;

  background-color: bisque;
  border-radius: 10px;

  div {
    display: flex;
    justify-content: center;
    align-items: center;

    background-color: burlywood;
    border-radius: inherit;

    transition: all ease 0.2s;

    user-select: none;
    font-size: calc((60vw - 50px) / 12);

    &.close {
      color: transparent;
    }
    &.disappear {
      transition: all ease 0.3s;
      transform: scale(0);
      visibility: hidden;
    }
    &.shake {
      animation: shaking;
      animation-duration: 0.5s;

      @keyframes shaking {
        10% {
          transform: translateX(2px);
        }
        20% {
          transform: translateX(-2px);
        }
        30% {
          transform: translateX(2px);
        }
        40% {
          transform: translateX(-2px);
        }
        50% {
          transform: translateX(2px);
        }
        60% {
          transform: translateX(-2px);
        }
        70% {
          transform: translateX(2px);
        }
        80% {
          transform: translateX(-2px);
        }
        90% {
          transform: translateX(2px);
        }
        100% {
          transform: translateX(0);
        }
      }
    }

    &.smoke {
      color: whitesmoke;
    }
    &.gray {
      color: gray;
    }
  }
}
</style>
