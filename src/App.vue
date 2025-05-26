<script setup>
import { ref } from "vue";
const cards = ref([]);
const cards_opened = ref([]);
let card_count = ref(16);
let is_play = ref(0); //0=ไม่เล่น 1=เล่น
function startGame() {
  cards.value = [];
  cards_opened.value = [];
  is_play.value = 1;
  // เพิ่มindex และ status เพื่อนำไปบันทึกใน cards แล้วค่อยไปแสดงใน ui แล้วเอาค่าindexไปใส่ในpathรูปที่จะแสดง
  // index เอาเป็นตัวจับคู่รูป
  // status = 0 คือยังไม่เปิดรูป  1 คือเปิดรูปและ 2 คือจับคู่แล้ว
  for (var i = 1; i <= 8; i++) {
    cards.value.push({ index: i, status: 0 });
    cards.value.push({ index: i, status: 0 });
  }
  // Math.random() = เลขทศนิยมระหว่าง [0, 1) => ง่ายๆก็คือ 0.0000x ถึง 0.9999x
  // Math.random() * (i + 1) = ค่าระหว่าง 0 ถึง i.999
  // Math.floor(...) = ตัดทศนิยมทิ้ง ก็จะได้เลขจำนวนเต็มเก็บไว้ใน j
  for (var i = cards.value.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [cards.value[i], cards.value[j]] = [cards.value[j], cards.value[i]];
  }
}
function handleClick(card) {
  if (cards_opened.value.length < 2) {
    card.status = 1;
    cards_opened.value.push(card);
    if (cards_opened.value.length == 2) {
      setTimeout(checkCard, 1000);
    }
  }
}

function checkCard() {
  if (cards_opened.value[0].index === cards_opened.value[1].index) {
    cards_opened.value[0].status = 2;
    cards_opened.value[1].status = 2;
    card_count.value -= 2;
  } else {
    cards_opened.value[0].status = 0;
    cards_opened.value[1].status = 0;
  }
  cards_opened.value = [];
  if (card_count.value === 0) {
    is_play.value = 2;
  }
}
</script>

<template>
  <div
    class="h-screen select-none p-6 flex flex-col justify-center items-center gap-5 overflow-hidden"
  >
    <h1 class="text-center text-5xl font-bold geo-regular">Picture Matching</h1>
    <div v-if="is_play === 2" class="flex flex-col gap-2">
      <h1 class="text-center text-5xl font-bold geo-regular">You win</h1>
      <button
        @click="is_play = 0"
        class="text-center text-4xl font-bold geo-regular bg-amber-200 p-2 rounded-2xl cursor-pointer"
      >
        Okay
      </button>
    </div>

    <button
      v-if="is_play === 0"
      @click="startGame"
      class="text-center text-4xl font-bold geo-regular bg-amber-200 p-2 rounded-2xl cursor-pointer"
    >
      start
    </button>

    <div v-if="is_play === 1" class="grid grid-cols-4 content-evenly gap-5">
      <div v-for="card of cards" :key="card.id">
        <img
          class="w-[140px] rounded-2xl cursor-pointer"
          v-if="card.status === 0"
          src="/pictures/1.png"
          @click="handleClick(card)"
        />
        <img
          v-if="card.status === 1"
          :src="`/pictures/${card.index + 1}.png`"
          :alt="`Card ${card.index + 1}`"
          class="w-[140px] rounded-2xl cursor-pointer border-2"
        />
        <img
          v-if="card.status === 2"
          :src="`/pictures/${card.index + 1}.png`"
          :alt="`Card ${card.index + 1}`"
          class="w-[140px] rounded-2xl cursor-not-allowed border-2 opacity-70 saturate-0"
        />
        <div>{{ card.index }}</div>
      </div>
    </div>
  </div>
</template>

<style>
@import url("https://fonts.googleapis.com/css2?family=Geo:ital@0;1&display=swap");
.geo-regular {
  font-family: "Geo", sans-serif;
  font-weight: 400;
  font-style: normal;
}
</style>
