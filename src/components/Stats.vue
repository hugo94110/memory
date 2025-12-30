<script setup>
defineProps({
  timer: {
    type: Number,
    required: true
  },
  attempts: {
    type: Number,
    required: true
  },
  matched: {
    type: Number,
    required: true
  },
  total: {
    type: Number,
    required: true
  }
})
</script>

<template>
    <div class="statsContainer">
        <p>Essais : {{attempts}}</p>
        <p>Paires : {{matched}}</p>
        <p>Temps : {{timer}}s</p>
    </div>
</template>

<style scoped>
.statsContainer {
    display: flex;
    justify-content: center;
    gap: 20px;
    margin-top: 15px;
    padding: 0;
}
</style>