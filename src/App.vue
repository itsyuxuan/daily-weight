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

    <div v-if="weights && weights.length > 0">
      <h2>Last 7 days</h2>
      <div class="canvas-box">
        <canvas ref="weightCharEl" />

        <div class="weight-history">
          <h2>Weight History</h2>
          <ul>
            <li v-for="weight in weights" :key="weight.weight">
              <span>{{ weight.weight }}kg</span>
              <small>{{ new Date(weight.date).toLocaleDateString() }}</small>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </main>
</template>

<style scoped>

</style>
