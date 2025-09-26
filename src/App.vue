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
        id_tipo_energia: null,
        id_cidade: null,
        id_segmento: null,
        id_tipo_instalacao: null,
        gastoMensal: null,
        parametros: null
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
  },
  watch: {
    form: {
      deep: true,
      handler(newVal, oldVal) {
        if (
          newVal.id_cidade !== oldVal.id_cidade ||
          newVal.id_segmento !== oldVal.id_segmento ||
          newVal.id_tipo_instalacao !== oldVal.id_tipo_instalacao ||
          newVal.id_tipo_energia !== oldVal.id_tipo_energia
        ) {
          this.carregarParametros()
        }
      }
    }
  },
  methods: {
    async carregarParametros() {
      const { id_cidade, id_segmento, id_tipo_instalacao, id_tipo_energia } = this.form
      if (!id_cidade || !id_segmento || !id_tipo_instalacao || !id_tipo_energia) {
        this.form.parametros = null
        return
      }

      const qs = new URLSearchParams({
        id_cidade,
        incluir_eficiencias: '1',
        ef_segmento: id_segmento,
        ef_tipo_energia: id_tipo_energia,
        ef_tipo_instalacao: id_tipo_instalacao
      })

      try {
        const res = await fetch(`http://127.0.0.1:8000/api/parametros?${qs}`)
        if (!res.ok) throw new Error('Erro ao carregar parâmetros')
        this.form.parametros = await res.json()
      } catch (err) {
        console.error(err)
        this.form.parametros = null
      }
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
