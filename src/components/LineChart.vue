<template>
  <div class="card">
    <div class="card-body">
      <canvas ref="chartCanvas"></canvas>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, watch } from 'vue'
import { Chart, registerables } from 'chart.js'
import dayjs from 'dayjs'

Chart.register(...registerables)

export default {
  name: 'LineChart',
  props: {
    data: {
      type: Array,
      required: true
    },
    seriesNames: {
      type: Array,
      required: true
    }
  },
  setup(props) {
    const chartCanvas = ref(null)
    let chartInstance = null

    const createChart = () => {
      const labels = props.data.map(row =>
        dayjs(row.DateTime).format('DD-MM-YYYY HH:mm')
      )
      const datasets = props.seriesNames.map((series, index) => {
        const colors = ['red', 'blue', 'green']
        return {
          label: series,
          data: props.data.map(row => row.values[series]),
          borderColor: colors[index % colors.length],
          fill: false,
          tension: 0.1
        }
      })
      chartInstance = new Chart(chartCanvas.value, {
        type: 'line',
        data: {
          labels,
          datasets
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          scales: {
            x: {
              display: true,
              title: {
                display: true,
                text: 'DateTime'
              }
            },
            y: {
              display: true,
              title: {
                display: true,
                text: 'Price'
              }
            }
          }
        }
      })
    }

    const updateChart = () => {
      if (chartInstance) {
        chartInstance.data.labels = props.data.map(row =>
          dayjs(row.DateTime).format('DD-MM-YYYY HH:mm')
        )
        chartInstance.data.datasets = props.seriesNames.map((series, index) => {
          const colors = ['red', 'blue', 'green']
          return {
            label: series,
            data: props.data.map(row => row.values[series]),
            borderColor: colors[index % colors.length],
            fill: false,
            tension: 0.1
          }
        })
        chartInstance.update()
      }
    }

    onMounted(() => {
      createChart()
    })

    // Watch for changes in data or series visibility.
    watch(() => props.data, () => {
      updateChart()
    }, { deep: true })

    watch(() => props.seriesNames, () => {
      updateChart()
    }, { deep: true })

    return {
      chartCanvas
    }
  }
}
</script>

<style scoped>
.card {
  margin-bottom: 20px;
}
.card-body {
  height: 600px; /* Adjust as needed */
}
</style>
