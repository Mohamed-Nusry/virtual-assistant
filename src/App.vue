<template>
  <main id="app">
    <!-- Header -->
    <MainHeader v-if="agent && messages.length > 0" :agent="agent"></MainHeader>
    <section class="chat">

      <!-- Error message if any errors -->
      <p class="text-red" v-if="error">{{ error }}</p>

      <!-- Show home page -->
      <HomePage v-if="agent && messages.length == 0" :agent="agent" />

      <!-- Messages Table -->
      <section v-else aria-live="polite">
        <div v-for="message in messages" id="message" :key="message.responseId">
          <!-- My message -->
          <WrapperComponent me><BubbleComponent v-if="message.queryResult.queryText" :text="message.queryResult.queryText" me /></WrapperComponent>

          <!-- Dialogflow Components -->
          <WrapperComponent v-for="(component, component_id) in message.queryResult.fulfillmentMessages" :key="component_id" :fullwidth="false">

            <BubbleComponent v-if="component.text" :text="component.text.text[0] || 'No content'" />

            <BubbleComponent
              v-if="component.simpleResponses"
              :text="component.simpleResponses.simpleResponses[0].displayText || component.simpleResponses.simpleResponses[0].textToSpeech || 'No content'"
            />
          </WrapperComponent>

          <!-- Status Message -->
          <WrapperComponent><BubbleComponent v-if="message.queryResult.diagnosticInfo && message.queryResult.diagnosticInfo.end_conversation" :text="'End of Conversation'" /></WrapperComponent>
        </div>

        <div v-if="loading" id="message">
          <!-- My message (Loading) -->
          <WrapperComponent me><BubbleComponent aria-hidden="true" me loading /></WrapperComponent>
          <WrapperComponent><BubbleComponent aria-hidden="true" loading /></WrapperComponent>
        </div>
      </section>
    </section>

    <!-- Typing area  -->
    <ChatArea ref="input" @submit="send" @listening="stop_feedback" @typing="stop_feedback"></ChatArea>
  </main>
</template>

<style lang="sass">
@import '@/style/theme.sass'
@import '@/style/mixins'
</style>

<script>
import HomePage from '@/views/HomePage.vue'

import MainHeader from '@/components/MainHeader.vue'
import ChatArea from '@/components/ChatArea.vue'

import WrapperComponent from '@/components/WrapperComponent.vue'
import BubbleComponent from '@/components/BubbleComponent.vue'

import * as uuidv1 from 'uuid/v1'

import { Client } from 'dialogflow-gateway'

export default {
  name: 'App',
  components: {
    HomePage,
    MainHeader,
    ChatArea,
    WrapperComponent,
    BubbleComponent
  },
  data () {
    return {
      agent: null,
      messages: [],
      language: '',
      session: '',
      loading: false,
      error: null,
      client: new Client(this.config.endpoint)
    }
  },
  computed: {
  },
  watch: {
    /* This function is triggered, when request is started or finished */
    loading () {
      setTimeout(() => {
        const app = document.querySelector('#app') // prevent the whole page jumping to bottom, when using in iframe
        if (app.querySelector('#message')) {
          const message = app.querySelectorAll('#message')[app.querySelectorAll('#message').length - 1].offsetTop - 70
          window.scrollTo({ top: message, behavior: 'smooth' })
        }
      }, 2) // smoothly scroll #app down to the last message
    }

  },
  created () {
    this.session = uuidv1()

    this.client.get()
      .then(agent => {
        this.agent = agent
      })
      .catch(error => {
        this.error = error.message
      })
  },
  methods: {
    send (submission) {
      let request
      if (submission.text) {
        request = {
          session: this.session,
          queryInput: {
            text: {
              text: submission.text,
              languageCode: this.lang()
            }
          }
        }
      }

      this.stop_feedback()
      this.loading = true
      this.error = null

      /* Send the request to endpoint */
      this.client.send(request)
        .then(response => {
          this.messages.push(response)
          this.handle(response) // <- trigger the handle function (explanation below)
          if (this.debug()) console.log(response) // <- log responses in development mode
        })
        .catch(error => {
          this.error = error.message
        })
        .then(() => {
          this.loading = false
        })
    },

    handle (response) {
      /* Handle dialog end */
      if (response.queryResult.diagnosticInfo && response.queryResult.diagnosticInfo.end_conversation) {
        this.$refs.input.disabled = true
        this.$refs.input.microphone = false
        this.$refs.input.should_listen = false
      }

      let text = ''

      /* Dialogflow Text/SimpleResponses */
      for (const component in response.queryResult.fulfillmentMessages) {
        if (response.queryResult.fulfillmentMessages[component].text) text += `${response.queryResult.fulfillmentMessages[component].text.text[0]}. `
        if (response.queryResult.fulfillmentMessages[component].simpleResponses && response.queryResult.fulfillmentMessages[component].simpleResponses.simpleResponses[0].textToSpeech) text += `${response.queryResult.fulfillmentMessages[component].simpleResponses.simpleResponses[0].textToSpeech}. `
        if (response.queryResult.fulfillmentMessages[component].rbmText) text += `${response.queryResult.fulfillmentMessages[component].rbmText.text}. `
      }

      /* Actions on Google Simple response */
      if (response.queryResult.webhookPayload && response.queryResult.webhookPayload.google) {
        for (const component in response.queryResult.webhookPayload.google.richResponse.items) {
          if (response.queryResult.webhookPayload.google.richResponse.items[component].simpleResponse && response.queryResult.webhookPayload.google.richResponse.items[component].simpleResponse.textToSpeech) text += `${response.queryResult.webhookPayload.google.richResponse.items[component].simpleResponse.textToSpeech}. `
        }
      }
    },

    stop_feedback () {
      return true
    }
  }
}
</script>
