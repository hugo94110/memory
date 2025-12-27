<script setup>
import { ref, watch, computed, onUnmounted } from 'vue'
import GameBoard from './components/GameBoard.vue'
import GameButtons from './components/GameButtons.vue'
import Stats from './components/Stats.vue'

// nom des cartes
const cardsVal = ['test', 'test2', 'test3', 'test4', 'test5', 'test6', 'test7', 'test8'];

// mélange + création du deck de cartes
// https://stackoverflow.com/questions/2450954/how-to-randomize-shuffle-a-javascript-array
function shuffle(array) {
  let currentIndex = array.length;

  while (currentIndex != 0) {

    let randomIndex = Math.floor(Math.random() * currentIndex);
    currentIndex--;

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

const cardList = ref([]);

// Stats
const attempts = ref(0)
const timer = ref(0)
let timerI = null


const matched = computed(() => {
  return cardList.value.filter(card => card.matched).length/2
})

const total = computed(() => cardList.value.length/2)

const startTimer = () => {
  // calculer en secondes
  timer.value = 0
  timerI = setInterval(() => {
    timer.value++
  }, 1000)
}

const stopTimer = () => {
  if (timerI) {
    clearInterval(timerI)
    timerI = null
  }
}

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
      attempts.value++  // ajoute un essai
      
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
        }, 1500);
      }

      userSelection.value.length = 0;
    }
  },
  { deep: true }
);


// jeu
const game = ref(false);

const startGame = () => {
    game.value = true;
    attempts.value = 0;
    cardList.value = createDeck();
    startTimer();
};

const restartGame = () => {
    stopTimer();
    attempts.value = 0;
    cardList.value = createDeck();
    userSelection.value = [];
    userCanFlipCard.value = true;
    startTimer();
};

//
watch(matched, (newVal) => {
  if (newVal === total.value && newVal>0) {
    stopTimer()
  }
})
</script>

<template>
  <header>

  </header>

  <main>
    <h1>Memory Game</h1>
    <GameButtons :game="game" @start-game="startGame" @restart-game="restartGame" />
    <GameBoard v-if="game" :cardList="cardList" @flip-card="flipCard" />
    <Stats v-if="game" :timer="timer" :attempts="attempts" :matched="matched" :total="total" />
  </main>
</template>

<style scoped>

</style>
