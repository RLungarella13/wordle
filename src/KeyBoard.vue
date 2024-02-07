<template>
  <div id="keyboard">
    <div class="row" v-for="(row, index) in keyboardRows" :key="index">
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
  #keyboard {
    margin: 30px 8px 0;
    user-select: none;
  }
  .row {
    display: flex;
    width: 100%;
    margin: 0 auto 8px;
    touch-action: manipulation;
  }
  .spacer {
    flex: 0.5;
  }
  button {
    font-family: inherit;
    font-weight: bold;
    border: 0;
    padding: 0;
    margin: 0 6px 0 0;
    height: 58px;
    border-radius: 4px;
    cursor: pointer;
    user-select: none;
    background-color: #d3d6da;
    color: #444;
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
    text-transform: uppercase;
  }
  button:hover {
    background-color: #c1c4c8;
  }
  button.big {
    flex: 1.5;
  }
</style>
