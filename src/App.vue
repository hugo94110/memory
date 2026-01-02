<script setup>
import { ref, watch, computed, onMounted } from 'vue'
import GameBoard from './components/GameBoard.vue'
import GameButtons from './components/GameButtons.vue'
import Stats from './components/Stats.vue'
import Difficulty from './components/Difficulty.vue'
import NicknameModal from './components/NicknameModal.vue'
import GameHistory from './components/GameHistory.vue'

const allCards = ['snake', 'resident', 'red_dead2', 'persona', 'night_reign', 'monsterhunter', 'Minecraft', 'kingdom2', 'human', 'gta6', 'gta', 'Forza', 'FC26', 'expedition', 'DS3', 'chronos', 'BF6', '2k'];

// difficulté par défaut = 4x4
const difficulty = ref(1)

const gridConfig = computed(() => {
  if (difficulty.value === 1) {
    return { cols: 4, totalCards: 16 }
  }
  else if (difficulty.value === 2) {
    return { cols: 5, totalCards: 20 }
  }
  else if (difficulty.value === 3) {
    return { cols: 6, totalCards: 36 }
  }
  else {
    return { cols: 4, totalCards: 16 }
  }
})

const nbPairs = computed(() => gridConfig.value.totalCards / 2)

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
  const cardsVal = allCards.slice(0, nbPairs.value)
  

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

// stats
const attempts = ref(0)
const timer = ref(0)
let timerI = null

const matched = computed(() => {
  return cardList.value.filter(card => card.matched).length / 2
})

const total = computed(() => cardList.value.length / 2)

const startTimer = () => {
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




// 
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
    attempts.value++

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
  userCanFlipCard.value = false;
  
  // cacher les cartes
  cardList.value.forEach(card => {
    card.visible = false;
    card.matched = false;
  });
  
  // attendre 500ms pour réafficher
  setTimeout(() => {
    attempts.value = 0;
    cardList.value = createDeck();
    userSelection.value = [];
    userCanFlipCard.value = true;
    startTimer();
  }, 500);
};




const changeDifficulty = (newDifficulty) => {
  difficulty.value = newDifficulty
  if (game.value) {
    stopTimer()
    attempts.value = 0
    userSelection.value = []
    userCanFlipCard.value = true
    cardList.value = createDeck()
    startTimer()
  }
}



// gestion de l'historique + pseudo
const gameHistory = ref([])
const showNicknameModal = ref(false)

onMounted(() => {
  const saved = localStorage.getItem('memoryGameHistory')
  if (saved) {
    gameHistory.value = JSON.parse(saved)
  }
})

const saveHistory = () => {
  localStorage.setItem('memoryGameHistory', JSON.stringify(gameHistory.value))
}

const addToHistory = (nickname) => {
  const gameData = {
    id: Date.now(),
    nickname: nickname,
    difficulty: difficulty.value,
    timer: timer.value,
    attempts: attempts.value,
    date: new Date().toISOString()
  }
  
  gameHistory.value.unshift(gameData)
  saveHistory()
  showNicknameModal.value = false
}

const deleteFromHistory = (gameId) => {
  if (confirm('Voulez-vous vraiment supprimer cette partie ?')) {
    gameHistory.value = gameHistory.value.filter(game => game.id !== gameId)
    saveHistory()
  }
}

const deleteAllHistory = () => {
  if (confirm('Voulez-vous vraiment supprimer tout l\'historique ? Cette action est irréversible.')) {
    gameHistory.value = []
    saveHistory()
  }
}

const editNickname = (gameId, newNickname) => {
  const game = gameHistory.value.find(g => g.id === gameId)
  if (game) {
    game.nickname = newNickname
    saveHistory()
  }
}

watch(matched, (newVal) => {
  if (newVal === total.value && newVal > 0) {
    stopTimer()
    setTimeout(() => {
      showNicknameModal.value = true
    }, 500)
  }
})
</script>

<template>
  <header>

  </header>

  <main>
    <h1>Memory Game</h1>
    <Difficulty @change-difficulty="changeDifficulty" :current-difficulty="difficulty" />
    <GameButtons :game="game" @start-game="startGame" @restart-game="restartGame" />
    <Stats v-if="game" :timer="timer" :attempts="attempts" :matched="matched" />
    <GameBoard v-if="game" :cardList="cardList" @flip-card="flipCard" :grid-cols="gridConfig.cols" />
    <NicknameModal :show="showNicknameModal" @submit="addToHistory"/>
    <GameHistory v-if="!game || matched === total" :history="gameHistory" @delete="deleteFromHistory" @deleteAll="deleteAllHistory" @edit-nickname="editNickname"/>
  </main>
</template>

<style scoped></style>