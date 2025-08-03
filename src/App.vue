<template>
  <div id="app">
    <h1>Calculadora Energia Sustentável</h1>
    <Stepper :step="step" />
    
    <component
      :is="currentComponent"
      v-model="form"
      @next="step++"
      @prev="step--"
    />
    
    <Results v-if="step === 5" :form="form" />
  </div>
</template>

<script>
import Stepper from './components/Stepper.vue'
import StepEnergyType from './components/Steps/StepEnergyType.vue'
import StepLocation from './components/Steps/StepLocation.vue'
import StepProfile from './components/Steps/StepProfile.vue'
import StepConsumption from './components/Steps/StepConsumption.vue'
import Results from './components/Results.vue'

export default {
  name: 'App',
  components: {
    Stepper,
    StepEnergyType,
    StepLocation,
    StepProfile,
    StepConsumption,
    Results
  },
  data() {
    return {
      step: 1,
      form: {
        energyType: null,      // 'solar' ou 'eolica'
        state: null,
        city: null,
        segment: null,         // 'residencial', 'comercial', ...
        installation: null,    // 'monofasico', ...
        expense: null,         // valor em R$
        kwh: null              // cálculo instantâneo
      }
    }
  },
  computed: {
    currentComponent() {
      return {
        1: 'StepEnergyType',
        2: 'StepLocation',
        3: 'StepProfile',
        4: 'StepConsumption'
      }[this.step]
    }
  }
}
</script>

<style lang="scss">
@import './assets/styles/main.scss';
#app {
  max-width: 600px;
  margin: 2rem auto;
  padding: 1rem;
}
</style>
