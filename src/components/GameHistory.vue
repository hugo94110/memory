<script setup>
import { ref } from 'vue'

const props = defineProps({
  history: {
    type: Array,
    required: true
  }
})

const emit = defineEmits(['delete', 'deleteAll', 'editNickname'])

const editingId = ref(null)
const editingNickname = ref('')

const startEdit = (game) => {
  editingId.value = game.id
  editingNickname.value = game.nickname
}

const cancelEdit = () => {
  editingId.value = null
  editingNickname.value = ''
}

const saveEdit = (gameId) => {
  if (editingNickname.value.trim()) {
    emit('editNickname', gameId, editingNickname.value.trim())
    cancelEdit()
  }
}

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
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="game in history" :key="game.id" class="historyRow">
            <td class="nicknameCell">
              <div v-if="editingId === game.id" class="editContainer">
                <input 
                  v-model="editingNickname" 
                  @keyup.enter="saveEdit(game.id)"
                  @keyup.esc="cancelEdit"
                  class="editInput"
                  maxlength="20"
                />
              </div>
              <span v-else>{{ game.nickname }}</span>
            </td>
            <td>{{ getDifficultyLabel(game.difficulty) }}</td>
            <td>{{ game.timer }}s</td>
            <td>{{ game.attempts }}</td>
            <td class="actionsCell">
              <div v-if="editingId === game.id" class="actionButtons">
                <button @click="saveEdit(game.id)" class="btnSave">✓</button>
                <button @click="cancelEdit" class="btnCancel">✕</button>
              </div>
              <div v-else class="actionButtons">
                <button @click="startEdit(game)" class="btnEdit">Modifier</button>
                <button @click="emit('delete', game.id)" class="btnDelete">Supprimer</button>
              </div>
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

.actionsCell {
  text-align: right;
}

.actionButtons {
  display: flex;
  gap: 8px;
  justify-content: flex-end;
}

.btnEdit, .btnSave, .btnCancel {
  padding: 8px 16px;
  background-color: #282828;
  color: white;
  border: none;
  border-radius: 25px;
  cursor: pointer;
  font-size: 13px;
}

.btnEdit:hover, .btnSave:hover, .btnCancel:hover {
  background-color: #444444;
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

.editInput {
  padding: 6px 10px;
  border: none;
  border-radius: 8px;
  background: #282828;
  color: white;
  font-size: 14px;
  width: 120px;
}

.editInput:focus {
  outline: none;
  background: #333;
}

@media (max-width: 768px) {
  th, td {
    padding: 12px 8px;
    font-size: 13px;
  }
}
</style>
