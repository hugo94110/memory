<script setup>
const props = defineProps({
  history: {
    type: Array,
    required: true
  }
})

const emit = defineEmits(['delete', 'deleteAll'])

const getDifficultyLabel = (difficulty) => {
  if (difficulty === 1) {
    return '4×4'
  }
  else if (difficulty === 2) {
    return '5×4'
  }
  else if (difficulty === 3) {
    return '6×6'
  }
  else {
    return difficulty
  }
}

const formatDate = (dateString) => {
  const date = new Date(dateString)
  return date.toLocaleDateString('fr-FR', {
    day: '2-digit',
    month: '2-digit',
    year: 'numeric',
    hour: '2-digit',
    minute: '2-digit'
  })
}
</script>

<template>
  <div class="historyContainer">
    <div class="historyHeader">
      <h2>Historique des Parties</h2>
      <button 
        v-if="history.length > 0" 
        @click="emit('deleteAll')" 
        class="btnDeleteAll"
      >
        Tout supprimer
      </button>
    </div>

    <div v-if="history.length === 0" class="emptyHistory">
      <p>Aucune partie enregistrée pour le moment.</p>
    </div>

    <div v-else class="tableContainer">
      <table class="historyTable">
        <thead>
          <tr>
            <th>Pseudo</th>
            <th>Difficulté</th>
            <th>Temps</th>
            <th>Essais</th>
            <th>Date</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="game in history" :key="game.id" class="historyRow">
            <td class="nicknameCell">{{ game.nickname }}</td>
            <td>{{ getDifficultyLabel(game.difficulty) }}</td>
            <td>{{ game.timer }}s</td>
            <td>{{ game.attempts }}</td>
            <td class="dateCell">{{ formatDate(game.date) }}</td>
            <td>
              <button @click="emit('delete', game.id)" class="btnDelete">
                Supprimer
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<style scoped>
.historyContainer {
  margin-top: 40px;
  max-width: 900px;
  margin-left: auto;
  margin-right: auto;
}

.historyHeader {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

h2 {
  color: white;
  font-size: 24px;
  margin: 0;
}

.btnDeleteAll {
  padding: 8px 16px;
  background-color: #282828;
  color: white;
  border: none;
  border-radius: 25px;
  cursor: pointer;
}

.btnDeleteAll:hover {
  background-color: #444444;
}

.emptyHistory {
  text-align: center;
  padding: 40px;
  color: white;
  opacity: 0.6;
}

.tableContainer {
  overflow-x: auto;
  border-radius: 8px;
  background: rgba(40, 40, 40, 0.3);
}

.historyTable {
  width: 100%;
  border-collapse: collapse;
}

th {
  padding: 16px;
  text-align: left;
  font-size: 14px;
  color: white;
  background: rgba(40, 40, 40, 0.5);
}

.historyRow {
  border-bottom: 1px solid rgba(60, 60, 60, 0.3);
}

.historyRow:hover {
  background: rgba(40, 40, 40, 0.3);
}

td {
  padding: 16px;
  color: white;
  font-size: 14px;
}

.nicknameCell {
  font-weight: 600;
}

.dateCell {
  font-size: 13px;
  opacity: 0.6;
}

.btnDelete {
  padding: 8px 16px;
  background-color: #dc2626;
  color: white;
  border: none;
  border-radius: 25px;
  cursor: pointer;
  font-size: 13px;
}

.btnDelete:hover {
  background-color: #b91c1c;
}

@media (max-width: 768px) {
  th, td {
    padding: 12px 8px;
    font-size: 13px;
  }
}
</style>
