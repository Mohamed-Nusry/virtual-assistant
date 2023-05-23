<template>
  <div class="chat-area">
    <div class="chat-container">
      <div class="chat-area-flex">
        <!-- Text input -->
        <input
          v-model="query"
          class="chat-input"
          type="text"
          autofocus
          :placeholder="'Message'"
          :aria-label="'Message'"
          :disabled="disabled"
          @keypress.enter="submit({text: query})"
          @focus="$emit('typing')">

        <!-- Send message button -->
        <transition name="chat-button-animation" mode="out-in">
          <button
            key="send"
            class="chat-area-action"
            :title="'Send'"
            :aria-label="'Send'"
            :disabled="disabled"
            @click="submit({text: query})">
            <i class="material-icons" aria-hidden="true">send</i>
          </button>

        </transition>
      </div>
    </div>
  </div>
</template>

<style lang="sass" scoped>
@import '@/style/mixins'
</style>

<script>

export default {
  name: 'ChatArea',
  emits: [''],
  data () {
    return {
      query: '',
      disabled: false
    }
  },
  computed: {
  },
  watch: {
  },
  methods: {
    submit (submission) {
      if (submission.text && submission.text.length > 0) {
        this.$emit('submit', submission)
        this.query = ''
      }
    }
  }
}
</script>
