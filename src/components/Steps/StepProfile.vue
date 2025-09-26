<template>
  <div class="step">
    <p>Selecione o segmento e tipo de instalação</p>

    <div class="options">
      <button
        v-for="seg in segments"
        :key="seg.id"
        @click="selectSegment(seg.id)"
        :class="{selected: modelValue.id_segmento === seg.id}"
      >
        {{ seg.nome }}
      </button>
    </div>

    <div class="options">
      <button
        v-for="inst in installs"
        :key="inst.id"
        @click="selectInstall(inst.id)"
        :class="{selected: modelValue.id_tipo_instalacao === inst.id}"
      >
        {{ inst.nome }}
      </button>
    </div>

    <div class="buttons">
      <button @click="$emit('prev')">Voltar</button>
      <button
        :disabled="!modelValue.id_segmento || !modelValue.id_tipo_instalacao"
        @click="$emit('next')"
      >
        Próximo
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'StepProfile',
  props: ['modelValue'],
  data() {
    return {
      segments: [
        { id: 1, nome: 'Residencial' },
        { id: 2, nome: 'Comercial' },
        { id: 3, nome: 'Industrial' },
        { id: 4, nome: 'Rural' }
      ],
      installs: [
        { id: 1, nome: 'Monofásico' },
        { id: 2, nome: 'Bifásico' },
        { id: 3, nome: 'Trifásico' }
      ]
    }
  },
  methods: {
    selectSegment(id) {
      this.$emit('update:modelValue', { 
        ...this.modelValue, 
        id_segmento: id 
      })
    },
    selectInstall(id) {
      this.$emit('update:modelValue', { 
        ...this.modelValue, 
        id_tipo_instalacao: id 
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.options {
  display: flex;
  gap: .5rem;
  flex-wrap: wrap;
  margin: 1rem 0;
  button {
    padding:.5rem 1rem;
    border:2px solid #fff;
    background:transparent;
    &.selected { background:#27ae60; color:#fff; }
  }
}
.buttons { display:flex; justify-content:space-between; }
</style>
