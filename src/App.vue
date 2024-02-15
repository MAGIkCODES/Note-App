<script setup>
  import { ref, onMounted } from 'vue';

  const showModal = ref(false)
  // const newNote = ref("");
  const newNoteTitle = ref("")
  const newNoteContent = ref("")
  const errorMessage = ref ("");
  const notes = ref([]);
  

  function getRandomColor() {
    return "hsl(" + Math.random() * 360 + ", 100%, 75%)";
   
  }

  const addNote = () => {
    if(newNoteContent.value.length < 10)
    // if (newNoteTitle.value.length === 0 || newNoteContent.value.length === 0 )
    return errorMessage.value = 'Notes needs to be more than ten characters'
    notes.value.unshift({
      id: Math.floor(Math.random() * 1000000),
      title: newNoteTitle.value,
      text: newNoteContent.value,
      date: new Date(),
      backgroundColor: getRandomColor()
    }),

    saveNotesTolocalStorage();
    showModal.value = false;
    newNoteTitle.value = '';
    newNoteContent.value = ''
    errorMessage.value = '';
  };

  const deleteNote = (id) => {
    notes.value = notes.value.filter(note => note.id !== id);
    saveNotesTolocalStorage();
  }

  const closeModal = () => {
    showModal.value = false;
    newNoteTitle.value = '';
    newNoteContent.value = '';
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
        <input v-model="newNoteTitle" type="text" placeholder="Note Title">
        <textarea v-model.trim="newNoteContent" name="note" id="note" cols="30" rows="10"></textarea>
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
         <h2>{{ note.title ? note.title : 'Add Title' }}</h2>
          <p class="main-text">{{note.text}}</p>
          <div class="modify-note">
            <p class="date">{{ note.date.toLocaleDateString("en-US") }}</p>
            <button @click="deleteNote(note.id)">delete</button>
            <button>edit</button>
          </div>    
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
    width: 350px;
    height: 350px;
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

  .card h2 {
    
    padding: 10px;
    border-bottom: 1px solid rgb(20, 19, 19);
    /* background-color: none; */
    outline: none;
    text-transform: capitalize;
  }

  .main-text {
    color: #000;
    margin-bottom: 30%;
  }

  .date {
    font-weight: 600;
    color: #000;
  }

  .modify-note {
    display: flex;
    justify-content: flex-end;
  }

  .modify-note button {
    margin-right: 10px;
  }

  .card-container {
    display: flex;
    flex-wrap: wrap;
  }

  .overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100vh;
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

  .modal input {
    width: 100%;
    padding: 10px;
    border: none;
    outline: none;
    font-size: 20px;
    text-transform: capitalize;
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