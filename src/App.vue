<template>
  <div>
    <router-view></router-view>
    <van-tabbar v-model="activeTab" @change="onTabChange">
      <van-tabbar-item icon="home-o" name="Home">首页</van-tabbar-item>
      <van-tabbar-item icon="notes-o" name="Notes">笔记列表</van-tabbar-item>
    </van-tabbar>
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'
import { useRoute, useRouter } from 'vue-router'

const activeTab = ref('Home')
const router = useRouter()
const route = useRoute()

// 路由改变时，更新活动的 tab
watch(
  () => route.name,
  (newName) => {
    activeTab.value = newName as string
  },
)

const onTabChange = (newTab: string) => {
  router.push({ name: newTab })
}
</script>

<style>
/* 设置 Tabbar 样式以固定在底部 */
.van-tabbar {
  position: fixed;
  bottom: 0;
  width: 100%;
}
</style>
