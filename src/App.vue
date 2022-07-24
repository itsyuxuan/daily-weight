<script setup>
import { computed, nextTick, ref, shallowRef, watch } from 'vue'
import Chart from 'chart.js/auto'

const weights = ref([])
const weightChartEl = ref(null)
const weightChart = shallowRef(null)
const weightInput = ref(60.0)
const currentWeight = computed(() => {
  return (
    weights.value.slice(0).sort((a, b) => b.date - a.date)[0] || { weight: 0 }
  )
})

const addWeight = () => {
  weights.value.push({
    weight: weightInput.value.toFixed(1),
    date: new Date().getTime(),
  })
}

watch(
  weights,
  (newWeights) => {
    const ws = [...newWeights]
    if (weightChart.value) {
      weightChart.value.data.labels = ws
        .sort((a, b) => a.date - b.date)
        .map(w => new Date(w.date).toLocaleDateString())
        .slice(-7)
      weightChart.value.data.datasets[0].data = ws
        .sort((a, b) => a.date - b.date)
        .map(w => w.weight)
        .slice(-7)
      weightChart.value.update()
      return
    }

    nextTick(() => {
      weightChart.value = new Chart(weightChartEl.value.getContext('2d'), {
        type: 'line',
        data: {
          labels: ws
            .sort((a, b) => a.date - b.date)
            .map(w => new Date(w.date).toLocaleDateString()),
          datasets: [
            {
              label: 'Weight',
              data: ws.sort((a, b) => a.date - b.date).map(w => w.weight),
              backgroundColor: 'rgba(131, 175, 112, 0.2)',
              borderColor: 'rgb(186, 208, 175)',
              borderWidth: 1,
              fill: true,
            },
          ],
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
        },
      })
    })
  },
  { deep: true },
)
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
        <canvas ref="weightChartEl" />
      </div>

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
  </main>
</template>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  background-color: azure;
}
main {
  padding: 1.5rem;
}
h1 {
  font-size: 2em;
  text-align: center;
  margin-bottom: 2rem;
}
h2 {
  margin-bottom: 1rem;
  color: grey;
  font-weight: 400;
}
.current {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 200px;
  height: 200px;
  text-align: center;
  background-color: white;
  border-radius: 999px;
  box-shadow: 0 4px 12px aqua;
  border: 5px solid aquamarine;
  margin: 0 auto 2rem;
}
.current span {
  display: block;
  font-size: 2em;
  font-weight: bold;
  margin-bottom: 0.5rem;
}
.current small {
  color: grey;
  font-style: italic;
}
form {
  display: flex;
  margin-bottom: 2em;
  border: 1px solid lightgrey;
  border-radius: 0.5rem;
  overflow: hidden;
  transition: 200ms linear;
}
form:focus-within,
form:hover {
  border-color: lightseagreen;
  border-width: 2px;
}
form input[type="number"] {
  appearance: none;
  outline: none;
  border: none;
  background-color: white;
  flex: 1;
  padding: 1rem 1.5rem;
  font-size: 1.25rem;
}
form input[type="submit"] {
  appearance: none;
  outline: none;
  border: none;
  cursor: pointer;
  background-color: lightseagreen;
  padding: 0.5rem 1rem;
  color: white;
  font-size: 1.25rem;
  font-weight: 700;
  transition: 200ms linear;
  border-left: 3px solid transparent;
}
form input[type="submit"]:hover {
  background-color: white;
  color: lightseagreen;
  border-left-color: lightseagreen;
}
.canvas-box {
  width: 100%;
  max-width: 720px;
  background-color: white;
  padding: 1rem;
  border-radius: 0.5rem;
  box-shadow: 0 4px 12px aqua;
  margin-bottom: 2rem;
}
.weight-history ul {
  list-style: none;
  padding: 0;
  margin: 0;
}
.weight-history ul li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.5rem;
  cursor: pointer;
}
.weight-history ul li:nth-child(even) {
  background-color: #f1f1f1;
}
.weight-history ul li:hover {
  background-color: #bad0af;
}
.weight-history ul li:last-of-type {
  border-bottom: none;
}
.weight-history ul li span {
  display: block;
  font-size: 1.25rem;
  font-weight: 600;
  margin-right: 1rem;
}
.weight-history ul li small {
  color: grey;
  font-style: italic;
}
</style>
