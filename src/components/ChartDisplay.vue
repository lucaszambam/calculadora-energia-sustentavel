<template>
  <div class="charts">
    <div class="chart-card">
      <h3>Comparativo Mensal (R$)</h3>
      <canvas ref="barEl"></canvas>
    </div>

    <div class="chart-card">
      <h3>Distribuição Anual (R$)</h3>
      <canvas ref="pieEl"></canvas>
    </div>
  </div>
</template>

<script>
import { onMounted, onBeforeUnmount, ref, watch } from 'vue'
import Chart from 'chart.js/auto'

export default {
  name: 'ChartDisplay',
  props: {
    summary: {
      type: Object,
      required: true
      // { convMensal, renMensal, convAnual, renAnual, economiaAnual, co2AnoKg }
    }
  },
  setup(props) {
    const barEl = ref(null)
    const pieEl = ref(null)
    let barChart = null
    let pieChart = null

    const build = () => {
      destroy()

      // Bar (mensal)
      barChart = new Chart(barEl.value, {
        type: 'bar',
        data: {
          labels: ['Convencional (mês)', 'Renovável (mês)'],
          datasets: [{
            label: 'R$',
            data: [props.summary.convMensal || 0, props.summary.renMensal || 0]
          }]
        },
        options: {
          responsive: true,
          plugins: { legend: { display: false } },
          scales: { y: { beginAtZero: true } }
        }
      })

      // Pie (anual)
      pieChart = new Chart(pieEl.value, {
        type: 'doughnut',
        data: {
          labels: ['Convencional (12m)', 'Renovável (12m)', 'Economia (12m)'],
          datasets: [{
            data: [
              props.summary.convAnual || 0,
              props.summary.renAnual || 0,
              props.summary.economiaAnual || 0
            ]
          }]
        },
        options: {
          responsive: true,
          plugins: { legend: { position: 'bottom' } }
        }
      })
    }

    const destroy = () => {
      if (barChart) { barChart.destroy(); barChart = null }
      if (pieChart) { pieChart.destroy(); pieChart = null }
    }

    onMounted(build)
    onBeforeUnmount(destroy)

    watch(() => ({ ...props.summary }), build, { deep: true })

    return { barEl, pieEl }
  }
}
</script>

<style lang="scss" scoped>
.charts {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;

  .chart-card {
    background: #fff; border-radius: 6px; padding: 1rem; border: 1px solid #e3e7ea;
    h3 { margin: 0 0 .75rem 0; font-size: 1rem; }
  }
}

@media (max-width: 900px) {
  .charts { grid-template-columns: 1fr; }
}
</style>
