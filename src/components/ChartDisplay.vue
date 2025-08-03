<template>
  <canvas ref="chartCanvas"></canvas>
</template>

<script>
import { onMounted, ref } from 'vue'
import Chart from 'chart.js/auto'

export default {
  name: 'ChartDisplay',
  props: ['data'],
  setup(props) {
    const chartCanvas = ref(null)
    onMounted(() => {
      new Chart(chartCanvas.value, {
        type: 'bar',
        data: {
          labels: ['Economia Mensal', 'Economia Anual', 'CO₂ evitado'],
          datasets: [{
            label: 'Valores',
            data: [
              props.data.economiaMensal,
              props.data.economiaAnual,
              props.data.co2Evitar
            ],
            backgroundColor: ['rgba(39,174,96,0.6)','rgba(39,174,96,0.4)','rgba(39,174,96,0.2)']
          }]
        },
        options: {
          responsive: true,
          scales: { y: { beginAtZero: true } }
        }
      })
    })
    return { chartCanvas }
  }
}
</script>

<style lang="scss" scoped>
canvas { max-width: 100%; }
</style>
