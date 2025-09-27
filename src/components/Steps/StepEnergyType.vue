<template>
  <div>
    <p>Selecione o tipo de energia:</p>
    <div class="option-group">
      <button
        class="option"
        :class="{ 'is-active': modelValue.id_tipo_energia === 1 }"
        @click="select(1)"
      >Solar</button>

      <button
        class="option"
        :class="{ 'is-active': modelValue.id_tipo_energia === 2 }"
        @click="select(2)"
      >Eólica</button>
    </div>

    <button class="btn btn--primary" :disabled="!modelValue.id_tipo_energia" @click="$emit('next')">
      Próximo
    </button>
  </div>
</template>

<script>
export default {
  name: 'StepEnergyType',
  props: { modelValue: Object },
  methods: {
    select(id) {
      this.$emit('update:modelValue', { ...this.modelValue, id_tipo_energia: id })
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
