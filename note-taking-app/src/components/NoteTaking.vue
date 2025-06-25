<template>
  <div class="container mt-4">
    <h1 class="mb-4">Vue CRUD Note Taking App</h1>

    <div class="card mb-4">
      <div class="card-body">
        <h5 class="card-title">{{ editingNote ? 'Edit Note' : 'Add New Note' }}</h5>
        <form @submit.prevent="saveNote">
          <div class="mb-3">
            <label for="noteTitle" class="form-label">Title</label>
            <input
              v-model="currentNote.title"
              type="text"
              class="form-control"
              id="noteTitle"
              required
            />
          </div>
          <div class="mb-3">
            <label for="noteContent" class="form-label">Content</label>
            <textarea
              v-model="currentNote.content"
              class="form-control"
              id="noteContent"
              rows="4"
              required
            ></textarea>
          </div>
          <div class="d-flex gap-2">
            <button type="submit" class="btn btn-primary">
              {{ editingNote ? 'Update Note' : 'Add Note' }}
            </button>
            <button v-if="editingNote" type="button" class="btn btn-secondary" @click="cancelEdit">
              Cancel
            </button>
          </div>
        </form>
      </div>
    </div>

    <div class="row">
      <div v-if="notes.length === 0" class="col-12">
        <div class="alert alert-info" role="alert">No notes yet. Create your first note above!</div>
      </div>
      <div v-else class="col-md-6 col-lg-4 mb-3" v-for="note in notes" :key="note.id">
        <div class="card h-100">
          <div class="card-body">
            <h5 class="card-title">{{ note.title }}</h5>
            <p class="card-text">{{ note.content }}</p>
            <small class="text-muted">Created: {{ formatDate(note.createdAt) }}</small>
          </div>
          <div class="card-footer bg-transparent">
            <button @click="editNote(note)" class="btn btn-sm btn-outline-primary me-2">
              Edit
            </button>
            <button @click="deleteNote(note.id)" class="btn btn-sm btn-outline-danger">
              Delete
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive } from 'vue'

interface Note {
  id: number
  title: string
  content: string
  createdAt: Date
}

const notes = ref<Note[]>([])
const editingNote = ref<Note | null>(null)
const currentNote = reactive({
  title: '',
  content: '',
})

const saveNote = () => {
  if (editingNote.value) {
    const index = notes.value.findIndex((note) => note.id === editingNote.value!.id)
    if (index !== -1) {
      notes.value[index] = {
        ...editingNote.value,
        title: currentNote.title,
        content: currentNote.content,
      }
    }
    editingNote.value = null
  } else {
    const newNote: Note = {
      id: Date.now(),
      title: currentNote.title,
      content: currentNote.content,
      createdAt: new Date(),
    }
    notes.value.push(newNote)
  }

  currentNote.title = ''
  currentNote.content = ''
}

const editNote = (note: Note) => {
  editingNote.value = note
  currentNote.title = note.title
  currentNote.content = note.content
}

const deleteNote = (id: number) => {
  if (confirm('Are you sure you want to delete this note?')) {
    notes.value = notes.value.filter((note) => note.id !== id)
  }
}

const cancelEdit = () => {
  editingNote.value = null
  currentNote.title = ''
  currentNote.content = ''
}

const formatDate = (date: Date) => {
  return date.toLocaleDateString()
}
</script>
