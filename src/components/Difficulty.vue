<script setup>
import { defineProps, defineEmits } from 'vue'

const props = defineProps({
    currentDifficulty: {
        type: Number,
        default: 1
    }
})

const emit = defineEmits(['changeDifficulty'])

function setDifficulty(size) {
    emit('changeDifficulty', size)
}
</script>

<template>
    <div class="difficultyButtons">
        <button @click="setDifficulty(1)" :class="{ active: currentDifficulty === 1 }" class="btn">4×4</button>
        <button @click="setDifficulty(2)" :class="{ active: currentDifficulty === 2 }" class="btn">5×4</button>
        <button @click="setDifficulty(3)" :class="{ active: currentDifficulty === 3 }" class="btn">6×6</button>
    </div>
</template>

<style scoped>
.difficultyButtons {
    display: flex;
    gap: 16px;
    justify-content: center;
    margin: 20px 0;
}

.btn {
    padding: 5px 14px;
    font-size: 19px;
    font-weight: 500;
    border: 2px solid #282828;
    border-radius: 25px;
    cursor: pointer;
    background: transparent;
    color: white;
}

.btn:hover {
    border-color: #444444;
    background-color: #282828;
}

.btn.active {
    background: #282828;
    color: white;
    border-color: #444444;
}
</style>