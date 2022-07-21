<script setup>
import { computed, nextTick, ref, shallowRef, watch } from 'vue'
import Chart from 'chart.js/auto'

const weights = ref([])
const weightChartEl = ref(null)
const weightChart = shallowRef(null)
const weightInput = ref(60.0)
const currentWeight = computed(() => {
  return weights.value.slice(0).sort((a, b) => b.date - a.date)[0] || { weight: 0 }
})

const addWeight = () => {
  weights.value.push({
    weight: weightInput.value,
    date: new Date().getTime(),
  })
}
</script>

<template>
  <main>
    <h1>Weight Tracker</h1>
    <div class="current">
      <span>{{ currentWeight.weight }}</span>
      <small>Current weight (kg)</small>
    </div>

    <form @submit.prevent="addWeight">
      <input v-model="weightInput" type="number" step="0.1">
      <input type="submit" value="Add weight">
    </form>
  </main>
</template>

<style scoped>

</style>
