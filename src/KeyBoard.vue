<template>
  <div class="keyboard">
    <div class="keyboard-rows" v-for="(row, index) in keyboardRows" :key="index">
      <button
        @click="$emit('key', key)"
        v-for="(key, i) in row"
        :class="{
          absent: letterStates?.[key] === LetterState.ABSENT,
          present: letterStates?.[key] === LetterState.PRESENT,
          correct: letterStates?.[key] === LetterState.CORRECT,
          big: key === 'Backspace' || key === 'Enter',
        }"
        :key="i"
      >
        <div v-if="key !== 'Backspace'">
          {{ key }}
        </div>
        <div class="icon" v-else>
          <img src="./icons/backspace.svg" alt="" />
        </div>
      </button>
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
  .keyboard-rows {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
    text-transform: uppercase;
    font-weight: bold;
  }
  button {
    width: 60px;
    height: 60px;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 20px;
    font-size: 20px;
    margin: 1px;
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
  .icon {
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .big {
    width: auto;
  }
  @media screen and (max-width: 700px) {
    button {
      font-size: 3vw;
      padding: 3vw;
      width: 8vw;
      height: 8vw;
    }
  }
</style>
