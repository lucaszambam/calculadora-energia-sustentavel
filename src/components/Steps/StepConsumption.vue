<template>
  <div class="step">
    <p>Digite seu gasto mensal médio (R$):</p>

    <input
      type="number"
      step="0.01"
      min="0"
      v-model.number="localExpense"
    />

    <p v-if="kwh !== null">≈ {{ kwh.toFixed(0) }} kWh/mês</p>

    <div class="buttons">
      <button @click="$emit('prev')">Voltar</button>
      <button :disabled="!canProceed" @click="nextStep">Próximo</button>
    </div>
  </div>
</template>

<script>
import { watchEffect } from 'vue'
import cityData from '@/assets/cityData.json'

export default {
  name: 'StepConsumption',
  props: {
    modelValue: Object
  },
  data() {
    return {
      localExpense: this.modelValue.expense ?? null,
      kwh: null
    }
  },
  computed: {
    canProceed() {
      return this.localExpense > 0 && this.kwh !== null
    }
  },
  mounted() {
    watchEffect(() => {
      const state = this.modelValue.state
      const city = this.modelValue.city
      const tarifa = cityData?.[state]?.[city]?.tarifa

      console.log('🔎 Estado:', state)
      console.log('🔎 Cidade:', city)
      console.log('🔎 Tarifa:', tarifa)
      console.log('💰 Gasto informado:', this.localExpense)

      if (this.localExpense > 0 && tarifa) {
        this.kwh = this.localExpense / tarifa
        console.log('✅ kWh calculado:', this.kwh)
      } else {
        this.kwh = null
        console.log('❌ Dados insuficientes para cálculo')
      }
    })
  },
  methods: {
    nextStep() {
      this.$emit('update:modelValue', {
        ...this.modelValue,
        expense: this.localExpense,
        kwh: this.kwh
      })
      this.$emit('next')
    }
  }
}
</script>

<style lang="scss" scoped>
.step {
  text-align: center;

  input {
    width: 80%;
    padding: 0.5rem;
    margin: 1rem 0;
    border-radius: 4px;
    border: 1px solid #ccc;
    font-size: 1rem;
  }

  .buttons {
    display: flex;
    justify-content: space-between;
    margin-top: 1rem;
  }
}
</style>
