<template>
  <div>
    <VChart ref="chartRef" :option="chartOptions" style="width: 100%; height: 300px" />
  </div>
</template>

<script setup lang="ts">
import { computed, defineProps, onMounted, ref } from 'vue'
import { use } from 'echarts/core'
import { BarChart } from 'echarts/charts'
import { GridComponent, TooltipComponent, TitleComponent } from 'echarts/components'
import { CanvasRenderer } from 'echarts/renderers'
import { ECOption } from 'vue-echarts'

import VChart from 'vue-echarts'

// 注册所需的 ECharts 模块
use([BarChart, GridComponent, TooltipComponent, TitleComponent, CanvasRenderer])

// 定义笔记接口
interface GroupedNotes {
  id: number
  title: string
  content: string
}

// 从父组件接收笔记数据
const props = defineProps<{
  groupedNotes: Record<string, GroupedNotes[]>
}>()

// 计算 x 轴和 y 轴数据
const chartData = computed(() => {
  return Object.entries(props.groupedNotes)
    .map(([date, notes]) => ({
      date,
      count: notes.length,
    }))
    .sort((a, b) => a.date.localeCompare(b.date))
})

// ECharts 图表配置项
const chartOptions = computed<ECOption>(() => {
  // 打印出 chartData 数据
  console.log('chartData:', chartData.value)

  // 打印出 xAxis 数据和 series 数据，确保它们一致
  const xAxisData = chartData.value.map((item) => item.date)
  const seriesData = chartData.value.map((item) => item.count)
  console.log('xAxis Data:', xAxisData)
  console.log('Series Data:', seriesData)

  return {
    title: {
      text: '每日笔记条数统计',
      left: 'center',
    },
    tooltip: {
      trigger: 'axis',
      axisPointer: {
        type: 'shadow',
      },
      formatter: (params) => {
        // 打印 params，查看 tooltip 的值
        console.log(params)
        const { name, value } = params[0]
        return `${name ? name : '无日期'}<br/>条数: ${value}`
      },
    },
    xAxis: {
      type: 'category',
      data: xAxisData, // 确保传递的是正确的 x 轴数据
      axisLabel: {
        rotate: 45,
        interval: 0,
      },
    },
    yAxis: {
      type: 'value',
      name: '条数',
    },
    series: [
      {
        data: seriesData, // 确保传递的是正确的 series 数据
        type: 'bar',
        barWidth: '60%',
        color: '#5470c6',
      },
    ],
  }
})
</script>

<style scoped>
/* 自适应的容器 */
div {
  width: 100%;
  height: 100%;
}
</style>
