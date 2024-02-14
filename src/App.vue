<script setup>
  import { ref, onMounted } from 'vue';

  const showModal = ref(false)
  const newNote = ref("");
  const errorMessage = ref ("");
  const notes = ref([]);
  

  function getRandomColor() {
    return "hsl(" + Math.random() * 360 + ", 100%, 75%)";
   
  }

  const addNote = () => {
    if(newNote.value.length < 10)
    return errorMessage.value = 'Notes needs to be more than ten characters'
    notes.value.push({
      id: Math.floor(Math.random() * 1000000),
      text: newNote.value,
      date: new Date(),
      backgroundColor: getRandomColor()
    }),

    saveNotesTolocalStorage();
    showModal.value = false;
    newNote.value = '';
    errorMessage.value = '';
  };

  const closeModal = () => {
    showModal.value = false;
    newNote.value = '';
    errorMessage.value = '';
  };

  const saveNotesTolocalStorage = () => {
    localStorage.setItem('notes', JSON.stringify(notes.value));
  };

  const savedNoteFromLocalStorage = () => {
    const savedNotes = localStorage.getItem('notes');
    if (savedNotes) {
     const parseNote = JSON.parse(savedNotes);

      // Ensure that each note's date property is a Date object
      notes.value = parseNote.map(note => ({
        ...note,
        date: new Date(note.date)
      }))
    }
  };

  onMounted(() => {
    savedNoteFromLocalStorage();
  })
</script>


<template>
 <main>
    <div v-if="showModal" class="overlay">
      <div class="modal">
        <button @click="closeModal" class="close">&times;</button>
        <textarea v-model.trim="newNote" name="note" id="note" cols="30" rows="10"></textarea>
        <p v-if="errorMessage">{{ errorMessage }}</p>
        <button @click="addNote">Add Note</button>
      </div>
    </div>
    <div class="container">
      <header>
        <h1>Notes</h1>
        <button @click="showModal = true">+</button>
      </header>
      <div class="card-container">
        <div v-for="note in notes" :key="note.id" class="card" :style="{backgroundColor: note.backgroundColor}">
          <input type="text" placeholder="Note Title">
          <p class="main-text">{{note.text}}</p>
          <p class="date">{{ note.date.toLocaleDateString("en-US") }}</p>
        </div>
      </div>
    </div>
 </main>
</template>

<style scoped>
  main {
    height: 100vh;
    width: 100vw;
    font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
    position: relative;
  }

  .container {
    max-width: 85%;
    padding: 20px;
    margin: 0 auto ;
    /* position: relative; */
  }

  header {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    align-items: center;
  }

  h1 {
    font-size: 75px;
    font-weight: 600;
    margin-bottom: 25px;
    color: #fff;
  }

  header button {
    background: #fff;
    width: 50px;
    height: 50px;
    border-radius: 100%;
    border: none;
    font-size: 25px;
    font-weight: bold;
    cursor: pointer;
  }

  .card {
    width: 300px;
    height: 300px;
    background-color: rgb(237, 182, 44);
    padding: 5px;
    border-radius: 5px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    margin-right: 20px;
    margin-bottom: 20px;
    overflow-wrap: break-word;
    /* overflow-y: scroll; */
  }

  input {
    width: 100%;
    padding: 10px;
    border: none;
    background-color: aliceblue;
    outline: none;
    /* text-transform: capitalize; */
  }

  .main-text {
    color: #000;
    margin-bottom: 30%;
  }

  .date {
    font-weight: 600;
    color: #000;
  }

  .card-container {
    display: flex;
    flex-wrap: wrap;
  }

  .overlay {
    position: absolute;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, .77);
    display: flex;
    justify-content: center;
    align-items: center;
    /* z-index: 10; */
  }

  .modal {
    width: 700px;
    max-width: 85%;
    background-color: #fff;
    border-radius: 5px;
    padding: 30px;
    position: relative;
    display: flex;
    justify-content: center;
    /* align-items: center; */
    flex-direction: column;
  }

  #note {
    width: 100%;
    height: 100%;
    resize: none;
    /* border: none; */
    /* outline: none; */
    outline-color: grey;
    padding: 10px;
    box-sizing: border-box;
    font-size: 14px;
  }

  .modal p {
    color: red;
  }

  .modal button {
    padding: 10px 20px;
    font-size: 20px;
    width: 100%;
    background-color: blueviolet;
    border: 0;
    border-radius: 3px;
    color: #fff;
    margin-top: 15px;
    cursor: pointer;
  }

  .modal button:hover {
    background-color: rgb(33, 7, 59);
  }

  .modal .close {
    width: 20px;
    /* height: 50px; */
    left: 0;
    right: 10px;
    margin-left: 93%;
    background: none;
    border: none;
    font-size: 25px;
    cursor: pointer;
    color: black;
  }

  .modal .close:hover {
    background: none;
    color: red;
  }


  
</style>