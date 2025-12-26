<script setup>
import { ref, watch } from 'vue'
import Card from './components/Card.vue'
import GameBoard from './components/GameBoard.vue'

// nom des cartes
const cardsVal = ['test', 'test2', 'test3', 'test4', 'test5', 'test6', 'test7', 'test8']

// mélange + création du deck de cartes
// https://stackoverflow.com/questions/2450954/how-to-randomize-shuffle-a-javascript-array
function shuffle(array) {
  let currentIndex = array.length;

  // While there remain elements to shuffle...
  while (currentIndex != 0) {

    // Pick a remaining element...
    let randomIndex = Math.floor(Math.random() * currentIndex);
    currentIndex--;

    // And swap it with the current element.
    [array[currentIndex], array[randomIndex]] = [
      array[randomIndex], array[currentIndex]];
  }
  return array;
}

const createDeck = () => {
  const cards = []

  cardsVal.forEach(value => {
    cards.push({ value, visible: false, matched: false })
    cards.push({ value, visible: false, matched: false })
  })
  
  const melange = shuffle(cards)
  
  melange.forEach((card, index) => {
    card.position = index
  })
  
  return melange
}


const cardList = ref(createDeck())

// https://github.com/bencodezen/peek-a-vue
// gestion des cartes
const userSelection = ref([]);
const userCanFlipCard = ref(true);

const flipCard = payload => {
  if (userCanFlipCard.value) {
    cardList.value[payload.position].visible = true;

    if (userSelection.value[0]) {
      if (
        userSelection.value[0].position === payload.position &&
        userSelection.value[0].faceValue === payload.faceValue
      ) {
        return;
      } else {
        userSelection.value[1] = payload;
      }
    } else {
      userSelection.value[0] = payload;
    }
  } else {
    return;
  }
};

watch(userSelection, currentValue => {
    if (currentValue.length === 2) {
      const cardOne = currentValue[0];
      const cardTwo = currentValue[1];

      userCanFlipCard.value = false;

      if (cardOne.faceValue === cardTwo.faceValue) {
        cardList.value[cardOne.position].matched = true;
        cardList.value[cardTwo.position].matched = true;
        userCanFlipCard.value = true;
      } else {
        setTimeout(() => {
          cardList.value[cardOne.position].visible = false;
          cardList.value[cardTwo.position].visible = false;
          userCanFlipCard.value = true;
        }, 1800);
      }

      userSelection.value.length = 0;
    }
  },
  { deep: true }
);
</script>

<template>
  <header>

  </header>

  <main>
    <h1>Memory Game</h1>
    <GameBoard :cardList="cardList" @flip-card="flipCard" />
  </main>
</template>

<style scoped>

</style>
