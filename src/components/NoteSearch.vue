<template>
  <van-search v-model="searchText" placeholder="搜索笔记" @input="onSearch" />
</template>

<script setup lang="ts">
import { ref, defineEmits } from 'vue'

const emit = defineEmits<{ (e: 'search', text: string): void }>()
const searchText = ref('')

const onSearch = () => {
  emit('search', searchText.value)
}
</script>

<style scoped>
::v-deep .van-search__content {
  width: 100%;
  width: -webkit-fill-available;
}
</style>
