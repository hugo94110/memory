<script setup>
import { computed } from "vue";

const props = defineProps({
  matched: {
    type: Boolean,
    default: false
  },
  position: {
    type: Number,
    required: true
  },
  value: {
    type: String,
    required: true
  },
  visible: {
    type: Boolean,
    default: false
  }
});

const emits = defineEmits(["selectCard"]);

const flippedStyles = computed(() => {
  if (props.visible) {
    return "isFlipped";
  }
});

const selectCard = () => {
  if (!props.matched) {
    emits("selectCard", {
      position: props.position,
      faceValue: props.value
    });
  }
};

</script>

<template>
  <div class="gameCard" :class="{ isFlipped: visible }" @click="selectCard">
    <div class="cardFace isFront">
      <img class="cardImage" :src="`/images/${value}.jpg`" :alt="value">
    </div>
    <div class="cardFace isBack"></div>
  </div>
</template>

<style scoped>
.gameCard {
  width: 140px;
  height: 140px;
  cursor: pointer;
  background-color: #282828;
  border-radius: 25px;
  position: relative;
  transition: 0.5s transform ease-in;
  transform-style: preserve-3d;

}

.gameCard.isFlipped {
  transform: rotateY(180deg);
}

.cardFace {
  width: 100%;
  height: 100%;
  border-radius: 25px;
  color: white;
  position: absolute;
  backface-visibility: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
  object-fit: cover;
}

.isBack {
  /* background-color: ; */
  /* background-image: url('/images/cardBack.png');
  background-size: cover; */
}

.isFront {
  /* background-color: red; */
  transform: rotateY(180deg);
}

.cardImage {
  max-width: 100%;
}
</style>
