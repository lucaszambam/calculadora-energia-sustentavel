<template>
  <div class="step">
    <p>Selecione Estado e Cidade</p>

    <div class="fields">
      <select v-model="local.stateSigla" @change="onStateChange" class="select">
        <option disabled value="">Selecione um estado</option>
        <option v-for="uf in estados" :key="uf.sigla" :value="uf.sigla">
          {{ uf.nome }} ({{ uf.sigla }})
        </option>
      </select>

      <select v-model="local.cityId" :disabled="!local.stateSigla || loadingCities" class="select">
        <option disabled value="">{{ loadingCities ? 'Carregando...' : 'Selecione uma cidade' }}</option>
        <option v-for="c in cidades" :key="c.id_cidade" :value="c.id_cidade">
          {{ c.nome }}
        </option>
      </select>
    </div>

    <div class="buttons">
      <button @click="$emit('prev')">Voltar</button>
      <button
        :disabled="!local.cityId || loadingParametros"
        @click="apply"
      >
        {{ loadingParametros ? 'Buscando parâmetros...' : 'Próximo' }}
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'StepLocation',
  props: { modelValue: Object },
  data() {
    return {
      estados: [
        { sigla: 'AC', nome: 'Acre' }, { sigla: 'AL', nome: 'Alagoas' }, { sigla: 'AP', nome: 'Amapá' },
        { sigla: 'AM', nome: 'Amazonas' }, { sigla: 'BA', nome: 'Bahia' }, { sigla: 'CE', nome: 'Ceará' },
        { sigla: 'DF', nome: 'Distrito Federal' }, { sigla: 'ES', nome: 'Espírito Santo' }, { sigla: 'GO', nome: 'Goiás' },
        { sigla: 'MA', nome: 'Maranhão' }, { sigla: 'MT', nome: 'Mato Grosso' }, { sigla: 'MS', nome: 'Mato Grosso do Sul' },
        { sigla: 'MG', nome: 'Minas Gerais' }, { sigla: 'PA', nome: 'Pará' }, { sigla: 'PB', nome: 'Paraíba' },
        { sigla: 'PR', nome: 'Paraná' }, { sigla: 'PE', nome: 'Pernambuco' }, { sigla: 'PI', nome: 'Piauí' },
        { sigla: 'RJ', nome: 'Rio de Janeiro' }, { sigla: 'RN', nome: 'Rio Grande do Norte' }, { sigla: 'RS', nome: 'Rio Grande do Sul' },
        { sigla: 'RO', nome: 'Rondônia' }, { sigla: 'RR', nome: 'Roraima' }, { sigla: 'SC', nome: 'Santa Catarina' },
        { sigla: 'SP', nome: 'São Paulo' }, { sigla: 'SE', nome: 'Sergipe' }, { sigla: 'TO', nome: 'Tocantins' }
      ],
      cidades: [],
      local: {
        stateSigla: this.modelValue.stateSigla || '',
        cityId: this.modelValue.id_cidade || ''
      },
      loadingCities: false,
      loadingParametros: false
    }
  },
  methods: {
    async onStateChange() {
      this.local.cityId = ''
      this.cidades = []
      if (!this.local.stateSigla) return
      try {
        this.loadingCities = true
        const res = await fetch(`http://127.0.0.1:8000/api/cidades?estado_sigla=${this.local.stateSigla}`)
        if (!res.ok) throw new Error('Falha ao buscar cidades')
        this.cidades = await res.json()
      } catch (e) {
        console.error(e)
        this.cidades = []
      } finally {
        this.loadingCities = false
      }
    },

    apply() {
      if (!this.local.cityId) return
      const chosenCity = this.cidades.find(c => c.id_cidade === this.local.cityId)
      this.$emit('update:modelValue', {
        ...this.modelValue,
        stateSigla: this.local.stateSigla,
        id_cidade: this.local.cityId,
        cidadeNome: chosenCity?.nome || ''
      })
      this.$emit('next')
    }
  },
  mounted() {
    if (this.local.stateSigla) this.onStateChange()
  }
}
</script>

<style lang="scss" scoped>
.fields { display: flex; gap: 1rem; margin: 1rem 0;
  select { padding: .5rem; border-radius: 4px; }
}
.buttons { display: flex; justify-content: space-between; }
</style>
