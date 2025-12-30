<script setup>
import Card from "./Card.vue";
import { computed } from 'vue';

const props = defineProps({
  cardList: {
    type: Array,
    required: true
  },
  gridCols: {
    type: Number,
    default: 4
  }
});

const emits = defineEmits(["flipCard"]);

const selectCard = payload => {
  emits("flipCard", payload);
};


const gridColumns = computed(() => `repeat(${props.gridCols}, 140px)`);
</script>

<template>
  <transition-group tag="section" class="gameBoard" name="shuffleCard" :style="{ gridTemplateColumns: gridColumns }">
    <Card v-for="card in cardList" :key="card.position" :matched="card.matched" :value="card.value" :visible="card.visible" :position="card.position" @select-card="selectCard"/>
  </transition-group>
</template>

<style scoped>
.gameBoard {
  display: grid;
  gap: 20px;
  /* margin-top: 20px; */
  padding: 20px;
  justify-content: center;
  background-color: transparent;
}
</style>