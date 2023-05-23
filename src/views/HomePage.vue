<template>
  <div class="home-page">
    <!-- Chatbot Image -->
    <img  class="chatbot-icon" src="img/logo.png" alt="chatbot_logo">

    <!-- Chatbot Title -->
    <div class="chatbot-title">Tokki</div>

    <!-- Chatbot Description -->
    <div class="chatbot-description">Powerful virtual assistant chatbot to assist you. Say "Hi" to get started!</div>

  </div>
</template>

<style lang="sass" scoped>
@import '@/style/mixins'
</style>

<script>

export default {
  name: 'HomePage',
  props: {
    chatbot: {
      type: Object,
      required: true
    }
  },
  data () {
    return {
    }
  },
  watch: {
  },
  created () {
  },
  methods: {
  }
}
</script>
