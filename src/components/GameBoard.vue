<script setup>
import Card from "./Card.vue";

defineProps({
  cardList: {
    type: Array,
    required: true
  }
});

const emits = defineEmits(["flipCard"]);

const selectCard = payload => {
  emits("flipCard", payload);
};
</script>

<template>
  <transition-group tag="section" class="gameBoard" name="shuffleCard">
    <Card v-for="card in cardList" :key="card.position" :matched="card.matched" :value="card.value" :visible="card.visible" :position="card.position" @select-card="selectCard"/>
  </transition-group>
</template>

<style scoped>
.gameBoard {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 10px;
  margin-top: 15px;
  padding: 0;
  /* border-radius: 0 0 15px 15px; */
  background-color: transparent;
}
</style>