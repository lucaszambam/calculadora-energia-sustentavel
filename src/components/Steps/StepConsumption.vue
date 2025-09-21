<template>
  <div class="step">
    <p>Informe seu gasto médio mensal de energia (R$):</p>
    <input
      type="number"
      v-model.number="localExpense"
      placeholder="Ex: 350.00"
      min="1"
      step="0.01"
    />

    <p v-if="consumoKwh">Consumo estimado: {{ consumoKwh.toFixed(2) }} kWh/mês</p>

    <div class="buttons">
      <button @click="$emit('prev')">Voltar</button>
      <button :disabled="!localExpense" @click="apply">Próximo</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'StepConsumption',
  props: { modelValue: Object },
  data() {
    return { localExpense: this.modelValue.gastoMensal || null }
  },
  computed: {
    tarifaEfetiva() {
      if (!this.modelValue.parametros) return 0
      return this.modelValue.parametros.tarifa_base * (1 + this.modelValue.parametros.taxa_distribuicao)
    },
    consumoKwh() {
      if (this.localExpense > 0 && this.tarifaEfetiva > 0) {
        return this.localExpense / this.tarifaEfetiva
      }
      return 0
    }
  },
  methods: {
    apply() {
      this.$emit('update:modelValue', {
        ...this.modelValue,
        gastoMensal: this.localExpense,
        consumoKwh: this.consumoKwh
      })
      this.$emit('next')
    }
  }
}
</script>
