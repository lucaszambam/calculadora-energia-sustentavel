<template>
  <div class="stepper">
    <div
      v-for="n in 5"
      :key="n"
      :class="['step', { active: n === step, done: n < step }]"
    >{{ n }}</div>
  </div>
</template>

<script>
export default {
  name: 'Stepper',
  props: ['step']
}
</script>

<style lang="scss" scoped>
.stepper {
  display: flex;
  justify-content: space-between;
  margin-bottom: 1.5rem;
  .step {
    width: 2rem; height: 2rem;
    border: 2px solid #fff; border-radius: 50%;
    display: flex; align-items: center; justify-content: center;
    background: transparent;
    &.active { background: #27ae60; color: #fff; }
    &.done   { background: #ecf0f1; color: #2c3e50; }
  }
}
</style>
