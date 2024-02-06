<template>
  <div class="app">
    <Transition>
      <div class="toast" v-if="displayMessage">
        <div class="toast-content">
          {{ messageContent }}
        </div>
      </div>
    </Transition>
    <div class="top">
      <div class="header">
        WORDLE
        <hr />
      </div>
      <div class="board">
        <div
          class="row"
          :class="{ shake: shakeRowIndex === index }"
          v-for="(row, index) of board"
          :key="index"
        >
          <div
            class="tile"
            v-for="tile of row"
            :key="tile.letter"
            :class="{
              clicked: tile.letter && !tile.state,
              revealp: tile.state === LetterState.PRESENT,
              revealc: tile.state === LetterState.CORRECT,
              reveala: tile.state === LetterState.ABSENT,
            }"
          >
            {{ tile.letter }}
          </div>
        </div>
      </div>
    </div>
    <KeyBoard :letter-states="letterStates" :prova="number" @key="handleKeyboardInput" />
  </div>
</template>

<script setup lang="ts">
  import { computed, onUnmounted, ref, type Ref } from "vue";
  import { LetterState } from "./types";
  import { words } from "./words";
  import KeyBoard from "./KeyBoard.vue";

  const correctAnswer = getNewWord();
  function getNewWord() {
    const wordIndex = Math.floor(Math.random() * words.length);
    return words[wordIndex];
  }
  const number = ref(0);
  const letterStates: Ref<Record<string, LetterState>> = ref({});

  //BoardProps
  const board = ref(
    Array.from({ length: 6 }, () =>
      Array.from({ length: 5 }, () => ({
        letter: "",
        state: LetterState.INITIAL,
      })),
    ),
  );

  const currentRowIndex = ref(0);
  const currentRow = computed(() => board.value[currentRowIndex.value]);

  let allowInput = true;
  const onKeyUp = (e: KeyboardEvent) => handleKeyboardInput(e.key);

  window.addEventListener("keyup", onKeyUp);
  onUnmounted(() => {
    window.removeEventListener("keyup", onKeyUp);
  });
  function handleKeyboardInput(keyBoardInput: string) {
    if (!allowInput) return;
    if (keyBoardInput === "Backspace") {
      clearTile();
    } else if (keyBoardInput === "Enter") {
      evaluateGuess();
    } else if (/^[a-zA-Z]$/.test(keyBoardInput)) {
      fillTile(keyBoardInput);
    }
  }
  function fillTile(key: string) {
    for (const tile of currentRow.value) {
      if (!tile.letter) {
        tile.letter = key;
        break;
      }
    }
  }

  function clearTile() {
    for (const tile of [...currentRow.value].reverse()) {
      if (tile.letter) {
        tile.letter = "";
        break;
      }
    }
  }

  function evaluateGuess() {
    if (currentRow.value.every((tile) => tile.letter)) {
      const guess = currentRow.value.map((tile) => tile.letter).join("");
      if (!words.includes(guess)) {
        shakeYourBooty();
        showMessage("Word not in list!", 1);
      } else {
        evaluateLetters();
        currentRowIndex.value++;
        if (currentRowIndex.value > 5) {
          showMessage("You Lost: " + correctAnswer.toUpperCase(), 6);
          allowInput = false;
          return;
        }
      }
    } else {
      shakeYourBooty();
      showMessage("Word too short!", 1);
    }
  }

  //TODO add LetterStates to Keyboard
  function evaluateLetters() {
    applyRevelead();
    const correctLetters: (string | null)[] = correctAnswer.split("");
    //green ones
    let isCorrectGuess = true;
    currentRow.value.forEach((tile, i) => {
      if (tile.letter === correctLetters[i]) {
        tile.state = letterStates.value[tile.letter] = LetterState.CORRECT;
        correctLetters[i] = null;
      } else {
        isCorrectGuess = false;
      }
    });
    if (isCorrectGuess) {
      showMessage("You won", 6);
      allowInput = false;
      return;
    }
    //yellow ones
    currentRow.value.forEach((tile) => {
      if (!tile.state && correctLetters.includes(tile.letter)) {
        tile.state = letterStates.value[tile.letter] = LetterState.PRESENT;
        if (!letterStates.value[tile.letter]) {
          letterStates.value[tile.letter] = LetterState.PRESENT;
        }
        correctLetters[correctLetters.indexOf(tile.letter)] = null;
      }
    });
    //grey ones
    currentRow.value.forEach((tile) => {
      if (!tile.state) {
        tile.state = LetterState.ABSENT;
        if (!letterStates.value[tile.letter]) {
          letterStates.value[tile.letter] = LetterState.ABSENT;
        }
      }
    });
  }

  //animation js
  function applyRevelead() {
    let tiles = document.getElementsByClassName("tile");
    let tilesArray = Array.from(tiles);
    let start = 5 * currentRowIndex.value;
    tilesArray = tilesArray.splice(start, start + 5);
    tilesArray.map((tile: Element, i: number) => {
      let hTile = tile as HTMLElement;
      hTile.style.animationDelay = `${i * 50}ms`;
    });
  }
  const displayMessage: Ref<Boolean> = ref(false);
  const messageContent: Ref<String> = ref("");
  function showMessage(msg: string, timeout: number) {
    console.log(msg);
    displayMessage.value = true;
    messageContent.value = msg;
    setTimeout(() => {
      displayMessage.value = false;
    }, timeout * 1000);
  }

  const shakeRowIndex: Ref<Number> = ref(-1);

  function shakeYourBooty() {
    shakeRowIndex.value = currentRowIndex.value;
    setTimeout(() => {
      shakeRowIndex.value = -1;
    }, 1000);
  }
</script>

<style scoped>
  @import "./main.css";
  .app {
    height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: space-between;
  }
  .header {
    font-weight: bold;
    font-size: 6vh;
    color: white;
    margin: 2vh;
  }
  .board {
    margin-bottom: 2rem;
  }
  .row {
    display: flex;
    flex-direction: row;
  }
  .tile {
    position: relative;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: #111;
    border: 1px solid #444;
    margin: 0.2rem;
    width: 8vh;
    height: 8vh;
    color: white;
    font-size: 5vh;
    font-weight: bold;
    text-transform: uppercase;
    transition: 0.1s ease-in-out;
  }
  .clicked {
    border: 1px solid #888;
    transition: 0.4s ease-in-out;
    animation-name: jump;
    animation-duration: 0.1s;
  }
  .revealp {
    animation: flip-present 500ms ease forwards;
  }
  .reveala {
    animation: flip-absent 500ms ease forwards;
  }
  .revealc {
    animation: flip-correct 500ms ease forwards;
  }
  @keyframes flip-present {
    from {
      transform: scaleY(1);
    }
    50% {
      transform: scaleY(0);
    }
    to {
      transform: scaleY(1);
      background-color: var(--present);
      border: 1px solid var(--present);
    }
  }
  @keyframes flip-absent {
    from {
      transform: scaleY(1);
    }
    50% {
      transform: scaleY(0);
    }
    to {
      transform: scaleY(1);
      background-color: var(--absent);
      border: 1px solid var(--absent);
    }
  }
  @keyframes flip-correct {
    from {
      transform: scaleY(1);
    }
    50% {
      transform: scaleY(0);
    }
    to {
      transform: scaleY(1);
      background-color: var(--correct);
      border: 1px solid var(--correct);
    }
  }
  .toast {
    left: 0;
    right: 0;
    top: 50%;
    width: 15%;
    margin-left: auto;
    margin-right: auto;
    position: absolute;

    background-color: white;
    color: black;
    font-weight: bold;
    border-radius: 20px;
    padding: 1.5rem 0.5rem;
    z-index: 1;
  }
  .v-enter-active,
  .v-leave-active {
    transition: 0.3s ease-in-out;
  }

  .v-enter-from,
  .v-leave-to {
    transform: translateY(-50px);
    opacity: 0;
  }
  @keyframes jump {
    from {
      transform: scale(1);
    }
    75% {
      transform: scale(1.1);
    }
    to {
      transform: scale(1);
    }
  }
  .shake {
    animation-name: shake;
    animation-duration: 0.4s;
  }
  @keyframes shake {
    0% {
      transform: translate(4px);
    }
    20% {
      transform: translate(-4px);
    }
    40% {
      transform: translate(4px);
    }
    60% {
      transform: translate(-4px);
    }
    80% {
      transform: translate(4px);
    }
    100% {
      transform: translate(-4px);
    }
  }
  hr {
    margin: 0.4vh;
    border: 0.5px solid #444;
  }
  .top{
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: flex-start;
  }
</style>
