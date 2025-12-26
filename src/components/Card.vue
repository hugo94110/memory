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
  <div class="gameCard" @click="selectCard">
    <div v-if="visible" class="cardFace isFront">
      <img class="cardImage" :src="`/images/${value}.png`" :alt="value">
    </div>
    <div v-else class="cardFace isBack"></div>
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
}

.isBack {
  background-color: blue;
}

.isFront {
  background-color: red;
}

.cardImage {
  max-width: 100%;
}
</style>
