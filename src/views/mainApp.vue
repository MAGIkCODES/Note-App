<script setup>
import { ref, onMounted} from 'vue';
  

  const showModal = ref(false)
  const newNoteTitle = ref("")
  const newNoteContent = ref("")
  const errorMessage = ref ("");
  const notes = ref([]);
  const editingNote = ref(false);
  const isEditing = ref(false);
  const showConfirmationModal = ref(false);
  const showNoteModal = ref(false);
  const noteToDelete = ref('')

  const confirmDelete = () => {
  if (noteToDelete.value !== null) {
    notes.value = notes.value.filter(note => note.id !== noteToDelete.value);
    noteToDelete.value = null; // Reset noteToDelete after deletion
    saveNotesTolocalStorage();
  }
  showConfirmationModal.value = false;
};
const cancelDelete = () => {
  showConfirmationModal.value = false;
};

const deleteNoteModal = (id) => {
  noteToDelete.value = id; // Store the id of the note to be deleted
  showConfirmationModal.value = true;
};
  
  

 const getRandomColor = () => {
    return "hsl("+ Math.random() * 360 +", 100%, 75%)";
 }
  

  const addNote = () => {
    if (newNoteContent.value.length === 0 || newNoteContent.value.length < 10) {
    errorMessage.value = 'Note content must be at least ten characters';
    return;
  }
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
    // Check if the new content is empty
    if (newNoteContent.value.trim() === '') {
      errorMessage.value = 'Note content must be at least ten characters';
    } else if (newNoteContent.value.trim().length >= 10) { // Check if the new content meets the minimum length requirement
      editedNote.title = newNoteTitle.value;
      editedNote.text = newNoteContent.value;
      saveNotesTolocalStorage();
      closeModal();
    } else {
      errorMessage.value = 'Note content must be at least ten characters';
    }
  }
};

const closeModal = () => {
  showModal.value = false
  showNoteModal.value = false;
  newNoteTitle.value = '';
  newNoteContent.value = '';
  errorMessage.value = '';
  isEditing.value = false;
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
     <!-- Confirmation Modal -->
    <div v-if="showConfirmationModal" class="overlay">
      <div class="confirmation-modal">
        <p>Are you sure you want to delete this note?</p>
        <div class="buttons">
          <button class="bg-red" @click="confirmDelete">Yes</button>
          <button @click="cancelDelete">No</button>
        </div>
      </div>
    </div>

      <!-- Note Modal -->
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

     <!-- Note Cards -->
    <div class="container">
        <header>
            <h1>Notes</h1>
            <button @click="showModal =true">+</button>
        </header>
      <div class="card-container">
        <div v-for="note in notes" :key="note.id" class="card" :style="{backgroundColor: note.backgroundColor}">
         <h2>{{ note.title ? note.title : 'Note Title' }}</h2>
          <p class="main-text">{{note.text}}</p>
          <div class="modify-note">
            <p class="date">{{ note.date.toLocaleDateString("en-US") }}</p>
            <button @click="deleteNoteModal(note.id)">Delete</button>
            <button @click="editNoteModal(note)">Edit</button>
              <!-- <button @click="deleteNote(note.id)">Delete</button>
              <button @click=" editNoteModal(note)">Edit</button> -->
          </div>    
        </div>
      </div>
    </div>
 </main>
</template>