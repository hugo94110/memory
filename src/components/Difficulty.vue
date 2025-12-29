<script setup>
import { defineProps, defineEmits } from 'vue'

const props = defineProps({
    currentSize: {
        type: Number,
        default: 4
    }
})

const emit = defineEmits(['change-difficulty'])

function selectDifficulty(size) {
    emit('change-difficulty', size)
}
</script>

<template>
    <div class="difficulty-selector">
        <div class="buttons">
            <button @click="selectDifficulty(4)" :class="{ active: currentSize === 4 }" class="btn">4×4</button>

            <button @click="selectDifficulty(20)" :class="{ active: currentSize === 20 }" class="btn">5×4</button>

            <button @click="selectDifficulty(6)" :class="{ active: currentSize === 6 }" class="btn">6×6</button>
        </div>
    </div>
</template>

<style scoped>
.difficulty-selector {
    margin: 24px 0;
}

.buttons {
    display: flex;
    gap: 16px;
    justify-content: center;
}

.btn {
    padding: 0;
    width: 80px;
    height: 80px;
    font-size: 24px;
    font-weight: 600;
    border: 2px solid #e5e7eb;
    border-radius: 8px;
    cursor: pointer;
    transition: all 0.2s;
    background: white;
    color: gray;
}

.btn:hover {
    border-color: gray;
    color: gray;
}

.btn.active {
    background: gray;
    color: white;
    border-color: gray;
}
</style>