<template>
  <div>
    <p>Informe seu gasto médio mensal de energia (R$):</p>
    <div class="field mt-4 mb-6">
      <label class="label" for="gasto">Valor em reais</label>
      <input id="gasto" type="number" class="input" v-model.number="localExpense" min="1" step="0.01" placeholder="Ex: 350.00"/>
    </div>

    <div class="row">
      <button class="btn btn--ghost" @click="$emit('prev')">Voltar</button>
      <button class="btn btn--primary" :disabled="!localExpense" @click="apply">Próximo</button>
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
