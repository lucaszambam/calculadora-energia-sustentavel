<template>
  <div class="step results">
    <h2>Resultados</h2>
    <ul>
      <li>Convencional (mês): R$ {{ exp }}</li>
      <li>Convencional (12 meses): R$ {{ (exp*12).toFixed(2) }}</li>
      <li>Renovável (mês): R$ {{ renMensal.toFixed(2) }}</li>
      <li>Renovável (12 meses): R$ {{ renAnual.toFixed(2) }}</li>
      <li>Você economiza: R$ {{ (exp*12 - renAnual).toFixed(2) }}</li>
      <li>CO₂ evitado (kg/ano): {{ (exp*12 * 0.084).toFixed(0) }}</li>
    </ul>
    <ChartDisplay :data="{ convencional: exp, renovavel: renMensal, co2: exp*12*0.084 }"/>
  </div>
</template>

<script>
import ChartDisplay from './ChartDisplay.vue'
import cityData from '@/assets/cityData.json'
export default {
  name: 'Results',
  components: { ChartDisplay },
  props: ['form'],
  computed: {
    exp() { return this.form.expense },
    renMensal() {
      // ex: sistema considera kwh*tarifa com painel de 90% eficiência
      return this.form.kwh * cityData[this.form.state][this.form.city].tarifa * 0.10
    },
    renAnual() { return this.renMensal * 12 }
  }
}
</script>

<style lang="scss" scoped>
.results { background:#ecf0f1; padding:1rem; border-radius:6px;
  ul { list-style:none; padding:0; }
  li { margin:.5rem 0; }
}
</style>
