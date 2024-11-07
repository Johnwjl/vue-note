<template>
  <div>
    <van-field v-model="editableNote.title" label="Title" />
    <van-field v-model="editableNote.content" type="textarea" label="Content" />
    <van-button type="primary" @click="saveNote">Save</van-button>
  </div>
</template>

<script setup lang="ts">
import { ref, watch, defineProps, defineEmits } from 'vue'

interface Note {
  id: number
  title: string
  content: string
  date?: string
}

const props = defineProps<{ note: Note }>()
const emit = defineEmits<{ (e: 'save-note', note: Note): void }>()

const editableNote = ref({ ...props.note })

watch(
  () => props.note,
  (newNote) => {
    editableNote.value = { ...newNote }
  },
)

const saveNote = () => {
  emit('save-note', editableNote.value)
}
</script>
