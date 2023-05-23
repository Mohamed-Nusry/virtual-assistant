<template>
  <div class="wrapper-div-component" :class="{'me': me, 'fullwidth': fullwidth}">
    <slot />
  </div>
</template>

<style lang="sass">
@import '@/style/mixins'
</style>

<script>
export default {
  name: 'WrapperComponent',
  props: {
    me: {
      type: Boolean
    },
    fullwidth: {
      type: Boolean
    }
  }
}
</script>
