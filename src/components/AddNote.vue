<template>
  <div>
    <van-field v-model="newNoteTitle" label="标题" />
    <van-field v-model="newNoteContent" type="textarea" label="内容" />
    <van-button type="success" @click="addNote">新建笔记</van-button>
  </div>
</template>

<script setup lang="ts">
import { ref, defineEmits } from 'vue'

const emit = defineEmits<{
  (e: 'add-note', note: { id: number; title: string; content: string }): void
}>()

const newNoteTitle = ref('')
const newNoteContent = ref('')

const addNote = () => {
  const newNote = {
    id: Date.now(),
    title: newNoteTitle.value,
    content: newNoteContent.value,
  }
  emit('add-note', newNote)
  newNoteTitle.value = ''
  newNoteContent.value = ''
}
</script>
