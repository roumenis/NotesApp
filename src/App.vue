<script setup>
  import {ref} from "vue";

  const showModal = ref(false)
  const newNote = ref("") 
  const notes = ref([])
  const errorMessage = ref("")
  const deleteNote = (id) => {
    notes.value = notes.value.filter(note => note.id !== id); /* Smaže poznámku podle ID */
  }
  const customColor = ref(""); /* Prázdná = použije se generátor */
  function getRandonColor() { /* Stackoverflow funkce, přidava ke kartam random světlé barvy */
    return "hsl(" + Math.random() * 360 + ", 100%, 75%)";
  }

  const addNote = () => {
  if (newNote.value.length < 9) { // Kontrola délky poznámky
    return errorMessage.value = "Note must have more than 10 characters";
  }

  notes.value.push({
    id: Math.floor(Math.random() * 1000000),
    text: newNote.value,
    date: new Date(),
    backgroundColor: customColor.value || getRandonColor(), // Vlastní nebo generovaná barva
  });

  showModal.value = false;
  newNote.value = "";
  customColor.value = ""; // Reset barvy po přidání
  errorMessage.value = "";
};

  // Pro testování, aby se poznámky zobrazily i bez přidání
  //notes.value = [
  //  { id: 1, text: "Test note 1", date: new Date(), backgroundColor: getRandonColor() },
  //  { id: 2, text: "Test note 2", date: new Date(), backgroundColor: getRandonColor() },
  //;
  
    
    
</script>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      items: [],
    };
  },
  created() {
    this.fetchItems();
  },
  methods: {
    async fetchItems() {
      try {
        const response = await axios.get('http://localhost:8000/api/items/');
        this.items = response.data;
      } catch (error) {
        console.error("There was an error fetching the data:", error);
      }
    },
  },
};
</script>

<!-- String = "" Pole = [] -->

<template>
  <main>
    <div v-if="showModal" class="overlay"> <!-- v-if schová kod pokud neni na hodnotě true. Alternativa v-show, kod jde vidět v pruzkumu -->
      <div class="modal">
        
        <label>
          Vyber barvu (nebo ponech prázdné pro náhodnou):
          <input type="color" v-model="customColor" />
        </label>

        <textarea v-model.trim="newNote" id="note" cols="30" rows="10" >
        </textarea> <!-- "trim" nebude mezeru počítat jako znak. -->
        <p v-if="errorMessage">{{ errorMessage }}</p>
        <button @click="addNote">Add note</button>
        <button class="close" @click="showModal = false">Close</button>
      </div>
    </div>
    <div class="container">
      <header>
        <h1>Notes</h1>
        <button @click="showModal = true">+</button>
      </header>
      <div class="cards-container">
        <div 
          v-for="note in notes"
          :key="note.id" 
          class="card" 
          :style="{backgroundColor: note.backgroundColor}"
        > <!-- :style = javascript -->
          
          <p class="main-text">{{ note.text }}</p>
          <p class="date">{{ note.date.toLocaleDateString("en-US") }}</p>
          <button @click="deleteNote(note.id)" class="delete-btn">Smazat</button>
        </div>
      </div>
    </div>

    <div>
    <h1>Item List</h1>
    <ul>
      <li v-for="item in items" :key="item.id">
        {{ item.name }}: {{ item.description }}
      </li>
    </ul>
  </div>

  </main>
</template>

<!-- scoped = stylování platí pouze pro tento App.vue -->
<style scoped> 
  main {
    background-color: rgb(206, 206, 206);
    height: 100vh;
    width: 100vw;
    color: black;
  }
  .container {
    max-width: 1000px;
    padding: 10px;
    margin: 0 auto;
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
    background-color: rgb(21,20,20);
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
    justify-content: space-between;
    margin-right: 20px;
    margin-bottom: 20px;
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
    background-color: rgba(0,0,0,0.77);
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
    background-color: rgb(57, 120, 255);
    border: none;
    color: white;
    cursor: pointer;
    margin-top: 10px;
  }
  .modal .close {
    background-color: rgb(255, 45, 45);
  }
  .modal p {
    color: red;
  }
  /* stylování mazacího tlačítka */
  .delete-btn {
  background-color: rgb(255, 80, 80);
  color: white;
  border: none;
  padding: 8px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 14px;
}
</style>

