<template>
    <div class="keyboard">
        <div class="keyboard-rows" v-for="(row, index) in keyboardRows" :key="index">
            <button class="key" @click="$emit('key', key)" v-for="(key, i) in row" :class="{
                absent: letterStates?.[key] === LetterState.ABSENT,
                present: letterStates?.[key] === LetterState.PRESENT,
                correct: letterStates?.[key] === LetterState.CORRECT,
                big: key === 'Backspace' || key === 'Enter',
            }" :key="i">
                {{ key }}
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
    width: 3rem;
    height: 3rem;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 1rem;
    font-size: 1.2rem;
    margin: 0.1rem;
    border: 0px;
    font-weight: bold;
    text-transform: uppercase;
    background-color: #ddd;
}

button:hover {
    background-color: #aaa;
}

button:active {
    background-color: #ddd;
}

.big {
    width: auto;
}
</style>
