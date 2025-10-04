<template>
  <div class="stepper">
    <div
      v-for="n in 4"
      :key="n"
      class="stepper__item"
      :class="{
        'stepper__item--active': n === step,
        'stepper__item--done': n < step
      }"
    >{{ n }}</div>
  </div>
</template>

<script>
export default {
  name: 'Stepper',
  props: ['step']
}
</script>