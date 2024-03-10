<script setup>
 import { ref, onMounted } from 'vue';
  

  const showModal = ref(false)
  const newNoteTitle = ref("")
  const newNoteContent = ref("")
  const errorMessage = ref ("");
  const notes = ref([]);
  const editingNote = ref(false);
  const isEditing = ref(false);
  

  

  const addNote = () => {
    if(newNoteContent.value.length < 10)
    if (newNoteTitle.value.length === 0 || newNoteContent.value.length === 0 )
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

  const editNoteModal = note => {
    editingNote.value = note.id;
    newNoteTitle.value = note.title;
    newNoteContent.value = note.text;
    showModal.value = true;
    isEditing.value = true; 
  }

  const editNote = () => {
  const editedNote = notes.value.find(note => note.id === editingNote.value);
  if (editedNote) {
    editedNote.title = newNoteTitle.value;
    editedNote.text = newNoteContent.value;
    saveNotesTolocalStorage();
    closeModal();
  }
};
  const closeModal = () => {
    showModal.value = false;
    newNoteTitle.value = '';
    newNoteContent.value = '';
    errorMessage.value = '';
    isEditing.value = false
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
        <button v-if="!isEditing" @click="addNote">Add Note</button>
        <button v-if="isEditing" @click="editNote">Save</button>
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
              <button @click="deleteNote(note.id)">Delete</button>
              <button @click=" editNoteModal(note)">Edit</button>
          </div>    
        </div>
      </div>
    </div>
 </main>
</template>