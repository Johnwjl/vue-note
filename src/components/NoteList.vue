<template>
  <van-cell-group>
    <template v-for="(notes, date) in groupedNotes" :key="date">
      <!-- 日期分隔标题 -->
      <van-cell :title="date" title-style="font-weight: bold; color: #888;" />
      <!-- 笔记列表 -->
      <van-swipe-cell
        v-for="note in notes"
        :key="note.id"
        style="width: 100%"
        :before-close="beforeClose"
        @close="onClose($event, date, note.id)"
      >
        <van-cell :title="note.title" @click="() => selectNote(note)" />
        <template #right>
          <van-button type="danger" square>删除</van-button>
        </template>
      </van-swipe-cell>
    </template>
  </van-cell-group>
</template>

<script setup lang="ts">
import { defineProps, defineEmits } from 'vue'

import { showConfirmDialog } from 'vant'

interface Note {
  id: number
  title: string
  content: string
  date?: string
}

defineProps<{
  groupedNotes: Record<string, Note[]>
}>()

const emit = defineEmits<{
  (e: 'select-note', note: Note): void
  (event: 'deleteNote', date: string, noteId: number): void
}>()

const selectNote = (note: Note) => {
  emit('select-note', note)
}

// 触发删除事件
const onClose = ({ position }: { position: string }, date: string, noteId: number) => {
  if (position === 'right') {
    console.log('onClose', position)
    emit('deleteNote', date, noteId)
  }
}

// position 为关闭时点击的位置
const beforeClose = ({ position }: { position: string }): boolean | Promise<boolean> => {
  switch (position) {
    case 'left':
    case 'cell':
    case 'outside':
      return true
    case 'right':
      return new Promise((resolve) => {
        console.log('beforeClose', position)
        showConfirmDialog({
          title: '确定删除吗？',
        })
          .then(() => resolve(true))
          .catch(() => resolve(false))
      })
    default:
      return false // 添加默认返回值，确保所有分支都有返回类型
  }
}
</script>
