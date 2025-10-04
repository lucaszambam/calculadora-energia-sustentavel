<template>
  <div class="charts">
    <div class="chart-card card card--pad" style="height:100%;">
      <h3>Comparativo Mensal (R$)</h3>
      <canvas ref="barEl" style="max-height:220px;"></canvas>
    </div>

    <div class="chart-card card card--pad" style="height:100%">
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
          maintainAspectRatio: false,
          plugins: { legend: { position: 'bottom' } },
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
  },
  watch: {
    summary: {
      deep: true,
      handler(newVal) {
        if (this.chart) {
          this.chart.data.datasets[0].data = [
            newVal.convMensal,
            newVal.renMensal
          ]
          this.chart.update()
        }
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.charts {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;

  .chart-card {
    background: #fff;
    border-radius: 6px;
    padding: 1rem;
    border: 1px solid #e3e7ea;
    width: 100%;
    max-width: 100%;

    canvas {
      width: 100% !important;
      height: auto !important;
    }

    h3 {
      margin: 0 0 .75rem 0;
      font-size: 1rem;
    }
  }
}

@media (max-width: 900px) {
  .charts {
    grid-template-columns: 1fr;
  }
}
</style>
