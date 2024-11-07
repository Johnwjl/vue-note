<template>
  <div>
    <NoteSearch @search="handleSearch" />
    <AddNote @add-note="handleAddNote" />
    <NoteList
      :groupedNotes="groupedNotes"
      :filterText="filterText"
      @select-note="handleSelectNote"
      @deleteNote="deleteNote"
    />
    <NoteEditor
      v-if="showEditor && selectedNote"
      :note="selectedNote"
      @save-note="handleSaveNote"
    />

    <!-- 每日笔记字数 统计图表 -->
    <DailyWordCountChart :notes="notes" :groupedNotes="groupedNotes" />
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import dayjs from 'dayjs' // 用于格式化日期
import NoteSearch from '../components/NoteSearch.vue'
import AddNote from '../components/AddNote.vue'
import NoteList from '../components/NoteList.vue'
import NoteEditor from '../components/NoteEditor.vue'
import DailyWordCountChart from '../components/DailyWordCountChart.vue'
import { useStorage } from '@vueuse/core' // 使用 VueUse 的本地存储管理工具

interface Note {
  id: number
  title: string
  content: string
  date: string
}

// 使用 VueUse 的 useStorage 实现本地存储，确保 notes 数据可以被持久化
const notes = useStorage<Note[]>('notes', [])

const selectedNote = ref<Note | null>(null)
const filterText = ref('')
const showEditor = ref(false)

// 添加笔记
const handleAddNote = (note: Note) => {
  notes.value.push(note)
}

const handleSelectNote = (note: Note) => {
  selectedNote.value = note
  showEditor.value = true
}
// 修改笔记 保存
const handleSaveNote = (note: Note) => {
  const index = notes.value.findIndex((n) => n.id === note.id)
  if (index !== -1) {
    notes.value[index] = { ...note }
  }
  showEditor.value = false
}

// 处理搜索
const handleSearch = (text: string) => {
  filterText.value = text
}

// 根据日期分组笔记
const groupedNotes = computed(() => {
  const groups: Record<string, Note[]> = {}
  notes.value
    .filter((note) => note.title.includes(filterText.value))
    .forEach((note) => {
      const formattedDate = dayjs(note.date).format('YYYY-MM-DD')
      if (!groups[formattedDate]) {
        groups[formattedDate] = []
      }
      groups[formattedDate].push(note)
    })

  return groups
})

// 删除笔记的方法
const deleteNote = (date: string, noteId: number) => {
  console.log('NoteListPage 父组件 deleteNote ', date, noteId)
  const noteIndex = notes.value.findIndex(
    (note) => note.id === noteId && dayjs(note.date).format('YYYY-MM-DD') === date,
  )
  if (noteIndex !== -1) {
    notes.value.splice(noteIndex, 1)
  }
}
</script>

<style scoped>
div {
  padding: 20px;
}
</style>
