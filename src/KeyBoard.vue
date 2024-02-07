<template>
  <div class="keyboard">
    <div class="keyboard-rows" v-for="(row, index) in keyboardRows" :key="index">
      <div class="spacer" v-if="index === 1"></div>
      <button
        @click="$emit('key', key)"
        v-for="(key, i) in row"
        :class="{
          absent: letterStates?.[key] === LetterState.ABSENT,
          present: letterStates?.[key] === LetterState.PRESENT,
          correct: letterStates?.[key] === LetterState.CORRECT,
          big: key === 'Backspace' || key === 'Enter',
          enter: key === 'Enter',
        }"
        :key="i"
      >
        <div v-if="key !== 'Backspace'">
          {{ key }}
        </div>
        <div class="backspace" v-else>
          <img src="./icons/backspace.svg" alt="" />
        </div>
      </button>
      <div class="spacer" v-if="index === 1"></div>
    </div>
  </div>
</template>

<script setup lang="ts">
  import { LetterState } from "./types";

  defineProps({
    letterStates: Object as () => Record<string, LetterState>,
    prova: Number,
  });
  defineEmits<{
    (e: "key", key: string): void;
  }>();
  const keyboardRows = [
    "qwertyuiop".split(""),
    "asdfghjkl".split(""),
    ["Enter", ..."zxcvbnm".split(""), "Backspace"],
  ];
</script>

<style scoped>
  .keyboard {
    width: 40%;
  }
  .keyboard-rows {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
    text-transform: uppercase;
    font-weight: bold;
    margin-bottom: 0.3vh;
  }
  button {
    height: 8vh;
    width: 8vw;
    font-size: 3.2vh;
    margin: 0.2vh;
    border-radius: 5px;
    display: flex;
    align-items: center;
    justify-content: center;
    border: 0px;
    font-weight: bold;
    text-transform: uppercase;
    color: #333;
    background-color: #ddd;
  }

  button:hover {
    background-color: #aaa;
  }

  button:active {
    background-color: #ddd;
  }
  .backspace {
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .backspace img {
    width: 3vh;
  }
  .enter {
    font-size: 2vh;
    padding: 0 20px;
  }
  .big {
    width: 12vw;
  }
  .spacer {
    width: 4vw;
  }
  @media screen and (max-width: 600px) {
    .keyboard {
      width: 100%;
    }
  }
</style>
