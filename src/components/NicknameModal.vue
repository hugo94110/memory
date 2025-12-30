<script setup>
import { ref } from 'vue'

const props = defineProps({
  show: {
    type: Boolean,
    required: true
  }
})

const emit = defineEmits(['submit'])

const nickname = ref('')

const handleSubmit = () => {
  if (nickname.value.trim()) {
    emit('submit', nickname.value.trim())
    nickname.value = ''
  }
}

const handleSkip = () => {
  emit('submit', 'Anonyme')
}
</script>

<template>
  <div v-if="show" class="modalOverlay" @click.self="handleSkip">
    <div class="modalContent">
      <h2>Félicitations !</h2>
      <p>Entrez votre pseudo pour enregistrer votre score :</p>
      <input 
        v-model="nickname" 
        type="text" 
        placeholder="Votre pseudo" 
        maxlength="20"
        @keyup.enter="handleSubmit"
        autofocus
      />
      <div class="modalButtons">
        <button @click="handleSubmit" class="btnPrimary" :disabled="!nickname.trim()">
          Enregistrer
        </button>
        <button @click="handleSkip" class="btnSecondary">
          Passer
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.modalOverlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.8);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modalContent {
  background: #1a1a1a;
  padding: 40px;
  border-radius: 25px;
  max-width: 400px;
  width: 90%;
  text-align: center;
}

h2 {
  color: white;
  margin-bottom: 10px;
  font-size: 24px;
}

p {
  color: rgba(255, 255, 255, 0.7);
  margin-bottom: 25px;
  font-size: 14px;
}

input {
  width: 100%;
  padding: 12px 16px;
  border: none;
  border-radius: 25px;
  font-size: 14px;
  background: #282828;
  color: white;
  margin-bottom: 25px;
  box-sizing: border-box;
}

input::placeholder {
  color: rgba(255, 255, 255, 0.5);
}

input:focus {
  outline: none;
  background: #333333;
}

.modalButtons {
  display: flex;
  gap: 15px;
  justify-content: center;
}

button {
  padding: 10px 25px;
  border: none;
  border-radius: 25px;
  font-size: 14px;
  cursor: pointer;
}

.btnPrimary {
  background: #282828;
  color: white;
}

.btnPrimary:hover:not(:disabled) {
  background: #444444;
}

.btnPrimary:disabled {
  opacity: 0.4;
  cursor: not-allowed;
}

.btnSecondary {
  background: transparent;
  color: rgba(255, 255, 255, 0.6);
}

.btnSecondary:hover {
  color: white;
}
</style>
