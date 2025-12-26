<script setup>

defineProps({
    game: {
        type: Boolean,
        required: true
    },
});

const emits = defineEmits(["startGame", "restartGame"]);

</script>

<template>
    <div class="buttonContainer" v-if="!game">
        <div class="startButton" @click="emits('startGame')">Lancer</div>
    </div>

    <div class="buttonContainer" v-else>
        <div class="restartButton" @click="emits('restartGame')">Redémarrer</div>
        <div class="resetButton">Réinitialiser</div>
    </div>

</template>

<style scoped>

.buttonContainer {
    display: flex;
    justify-content: center;
    gap: 20px;
    margin-top: 10px;
}

.startButton, .restartButton, .resetButton {
    cursor: pointer;
    background-color: #282828;
    border-radius: 25px;
    color: white;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 8px 25px;
}

.startButton:hover, .restartButton:hover, .resetButton:hover {
    background-color: #444444;
}
</style>