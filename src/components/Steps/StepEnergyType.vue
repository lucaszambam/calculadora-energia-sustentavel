<template>
  <div class="step">
    <p>Selecione o tipo de energia:</p>
    <div class="options">
      <button
        :class="{ selected: modelValue.energyType==='solar' }"
        @click="select('solar')"
      >Eólica</button>
      <button
        :class="{ selected: modelValue.energyType==='eolica' }"
        @click="select('eolica')"
      >Fotovoltaica</button>
    </div>
    <button :disabled="!modelValue.energyType" @click="$emit('next')">
      Próximo
    </button>
  </div>
</template>

<script>
export default {
  name: 'StepEnergyType',
  props: { modelValue: Object },
  methods: {
    select(val) {
      this.$emit('update:modelValue', { ...this.modelValue, energyType: val })
    }
  }
}
</script>

<style lang="scss" scoped>
.step { text-align: center;
  .options { display: flex; gap: 1rem; margin: 1rem 0;
    button {
      padding: .75rem 1.5rem; border: 2px solid #fff; background: transparent;
      &.selected { background: #27ae60; color: #fff; }
    }
  }
}
</style>
