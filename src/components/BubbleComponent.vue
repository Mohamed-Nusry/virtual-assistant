<template>
  <div class="bubble-view" tabindex="0" :class="{'me': me, 'loading': loading}">{{text}}</div>
</template>

<style lang="sass" scoped>
@import '@/style/mixins'
</style>

<script>
export default {
  name: 'BubbleComponent',
  props: {
    text: {
      type: String,
      required: false,
      default: ''
    },
    me: {
      type: Boolean
    },
    loading: {
      type: Boolean
    }
  }
}
</script>
