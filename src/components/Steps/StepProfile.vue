<template>
  <div class="step">
    <p>Selecione o segmento e tipo de instalação</p>
    <div class="options">
      <button v-for="seg in segments" :key="seg" @click="modelValue.segment=seg" :class="{selected: modelValue.segment===seg}">
        {{ seg }}
      </button>
    </div>
    <div class="options">
      <button v-for="inst in installs" :key="inst" @click="modelValue.installation=inst" :class="{selected: modelValue.installation===inst}">
        {{ inst }}
      </button>
    </div>
    <div class="buttons">
      <button @click="$emit('prev')">Voltar</button>
      <button :disabled="!modelValue.segment||!modelValue.installation" @click="$emit('next')">Próximo</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'StepProfile',
  props: ['modelValue'],
  data() {
    return {
      segments: ['Residencial','Comercial','Industrial','Rural'],
      installs: ['Monofásico','Bifásico','Trifásico']
    }
  }
}
</script>

<style lang="scss" scoped>
.options { display: flex; gap: .5rem; flex-wrap: wrap; margin: 1rem 0;
  button {
    padding:.5rem 1rem; border:2px solid #fff; background:transparent;
    &.selected { background:#27ae60; color:#fff; }
  }
}
.buttons { display:flex; justify-content:space-between; }
</style>
