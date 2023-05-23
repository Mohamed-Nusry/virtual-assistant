<template>
  <header class="main-header">
    <div class="container">
      <div class="main-header-container">
        <img class="main-header-icon" src="img/logo.png" alt="chatbot_logo">
        <div class="main-header-info">
          <div class="main-header-title">Tokki</div>
          <div class="main-header-subtitle">Powerful Virtual Assistant Chatbot</div>
        </div>
      </div>
    </div>
    <slot />
  </header>
</template>

<style lang="sass" scoped>
@import '@/style/mixins'
</style>

<script>
export default {
  name: 'MainHeader',
  props: {
    agent: {
      type: Object,
      default: null
    }
  }
}
</script>
