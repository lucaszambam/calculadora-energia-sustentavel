<template>
  <div class="step">
    <p>Selecione Estado e Cidade</p>
    <div class="fields">
      <select v-model="local.state">
        <option disabled value="">Selecione um estado</option>
        <option v-for="(cities, st) in data" :key="st" :value="st">{{ st }}</option>
      </select>
      <select v-model="local.city" :disabled="!local.state">
        <option disabled value="">Selecione uma cidade</option>
        <option v-for="city in Object.keys(data[local.state] ? data[local.state] : [])" :key="city" :value="city">
          {{ city }}
        </option>
      </select>
    </div>
    <div class="buttons">
      <button @click="$emit('prev')">Voltar</button>
      <button :disabled="!local.city" @click="apply()">Próximo</button>
    </div>
  </div>
</template>

<script>
import cityData from '@/assets/cityData.json'
export default {
  name: 'StepLocation',
  props: { modelValue: Object },
  data() {
    return {
      data: cityData,
      local: { state: this.modelValue.state, city: this.modelValue.city }
    }
  },
  methods: {
    apply() {
      const { state, city } = this.local
      this.$emit('update:modelValue', { ...this.modelValue, state, city })
      this.$emit('next')
    }
  }
}
</script>

<style lang="scss" scoped>
.fields { display: flex; gap: 1rem; margin: 1rem 0;
  select { padding: .5rem; border-radius:4px; }
}
.buttons { display: flex; justify-content: space-between; }
</style>
