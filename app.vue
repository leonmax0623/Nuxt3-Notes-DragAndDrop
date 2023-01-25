<script setup>

import { ref, onMounted, watch } from "vue";
import draggable from 'vuedraggable'

const showModal = ref(false)
const modalStatus = ref("Add")
const textContent = ref("")
const editingId = ref("")
const title = ref("")
const notes = ref([])
const errorMessage = ref("")
const drag = ref(false)

onMounted(() => {
  notes.value = JSON.parse(localStorage.getItem("notes")) || []
})

watch(notes, () => {
  localStorage.setItem("notes", JSON.stringify(notes.value))
})

function getRandomColor() {
  return "hsl(" + Math.random() * 360 + ", 100%, 75%)"
}

const handleNote = () => {
  if (title.value.length < 1) {
    return errorMessage.value = "Note title should not be empty!"
  }
  if (textContent.value.length < 10) {
    return errorMessage.value = "Note content needs to be 10 characters or more!"
  }

  if (modalStatus.value === "Edit") {
    notes.value.map((item) => {
      if (item.id === editingId.value) {
        item.title = title.value;
        item.text = textContent.value;
      }
    })
  } else {
    notes.value.push({
      id: Math.floor(Math.random() * 10000000),
      title: title.value,
      text: textContent.value,
      backgroundColor: getRandomColor()
    })
  }

  localStorage.setItem("notes", JSON.stringify(notes.value))

  showModal.value = false;
  textContent.value = "";
  title.value = "";
}

const addNote = () => {
  showModal.value = true;
  modalStatus.value = "Add";
  textContent.value = "";
  title.value = "";
}

const editNote = (note) => {
  showModal.value = true;
  textContent.value = note.text;
  title.value = note.title;
  modalStatus.value = "Edit";
  editingId.value = note.id;
}

const deleteNote = (id) => {
  const updatedNotes = notes.value.filter((item) => item.id !== id)
  notes.value = updatedNotes;
  localStorage.setItem("notes", JSON.stringify(updatedNotes))
}

</script>

<template>
  <main>
    <div v-if="showModal" class="overlay">
      <div class="modal">
        <h5>Note Title</h5>
        <input class="title" type="text" v-model="title" />
        <h5>Note Content</h5>
        <textarea v-model.trim="textContent" name="note" id="note" cols="30" rows="10"></textarea>
        <p v-if="errorMessage">{{ errorMessage }}</p>
        <button @click="handleNote">{{ modalStatus }} Note</button>
        <button @click="showModal = false" class="close">Close</button>
      </div>
    </div>
    <div class="container">
      <header>
        <h1>Notes</h1>
        <button @click="addNote">+</button>
      </header>
      <draggable class="cards-container" v-model="notes" group="people" @start="drag = true" @end="drag = false"
        item-key="id">
        <template #item="{ element }">
          <div class="card" :style="{ backgroundColor: element.backgroundColor }">
            <div class=" actions">
              <button @click="editNote(element)">Edit</button>
              <button @click="deleteNote(element.id)">Delete</button>
            </div>
            <p class="main-title">{{ element.title }}</p>
            <span class="main-text">{{ element.text }}</span>
          </div>
        </template>
      </draggable>
    </div>
  </main>
</template>

<style scoped>
main {
  height: 100vh;
  width: 100vw;
}

.container {
  max-width: 1000px;
  padding: 10px;
  margin: auto;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

h1 {
  font-weight: bold;
  margin-bottom: 25px;
  font-size: 75px;
}

header button {
  border: none;
  padding: 10px;
  width: 50px;
  height: 50px;
  cursor: pointer;
  background-color: rgb(21, 20, 20);
  border-radius: 100%;
  color: white;
  font-size: 20px;
}

.card {
  width: 225px;
  height: 225px;
  background-color: rgb(237, 182, 44);
  padding: 10px;
  border-radius: 15px;
  display: flex;
  flex-direction: column;
  margin-right: 20px;
  margin-bottom: 20px;
  cursor: pointer;
}

.title {
  height: 20px;
}

.main-title {
  font-size: 14px;
  font-weight: bold;
  text-decoration: underline;
}

.date {
  font-size: 12.5px;
  font-weight: bold;
}

.cards-container {
  display: flex;
  flex-wrap: wrap;
}

.overlay {
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.77);
  z-index: 10;
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal {
  width: 750px;
  background-color: white;
  border-radius: 10px;
  padding: 30px;
  position: relative;
  display: flex;
  flex-direction: column;
}

.modal button {
  padding: 10px 20px;
  font-size: 20px;
  width: 100%;
  background-color: blueviolet;
  border: none;
  color: white;
  cursor: pointer;
  margin-top: 15px;
}

.modal .close {
  background-color: rgb(193, 15, 15);
  margin-top: 7px;
}

.modal p {
  color: rgb(193, 15, 15);
}

.actions {
  display: flex;
  width: 100%;
  justify-content: space-between;
}

.actions button {
  font-size: 12px;
  background-color: rgb(255, 255, 255);
  border: none;
  color: black;
  cursor: pointer;
  border-radius: 10px;
  font-weight: bold;
  margin-top: 15px;
}
</style>

