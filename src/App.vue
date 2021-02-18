<template lang="pug">
  <div>
    #app
    VueBotUI(
    :options="botOptions",
    :messages="messageData",
    :bot-typing="botTyping",
    :input-disable="inputDisable",
    :is-open="false",
    @init="botStart",
    @msg-send="msgSend",
  )
    <div style="width: 100%; height:800px;background-color: rgb(226, 225, 225);"></div>
    <div style="width: 100%; height:800px;background-color: rgb(248, 250, 228);"></div>
    <div style="width: 100%; height:800px;background-color: rgb(250, 228, 246);"></div>
  </div>
</template>
<script>
import BotIcon from './assets/icons/bot-icon.png'
import { VueBotUI } from './vue-bot-ui'
import { messageService } from './helpers/message'

export default {
  components: {
    BotIcon,
    VueBotUI
  },

  data () {
    return {
      messageData: [
        {
          'agent': 'bot',
          'type': 'text',
          'text': 'Hello. Have a nice day!',
          'disableInput': false
        },
        {
          'agent': 'bot',
          'type': 'button',
          'text': 'How can we help you today?',
          'options': [
            {
              'text': 'Search Suport Articles',
              'value': 'search',
              'action': 'postback'
            },
            {
              'text': 'Submit Support Ticket',
              'value': 'submit_ticket',
              'action': 'postback'
            }
          ],
          'disableInput': true
        }
      ],
      botTyping: false,
      inputDisable: false,
      botOptions: {
        botTitle: 'My bot',
        colorScheme: '#1b53d0',
        textColor: '#fff',
        bubbleBtnSize: 56,
        animation: true,
        boardContentBg: '#fafafa',
        botAvatarSize: 37,
        botAvatarImg: BotIcon,
        msgBubbleBgBot: '#fff',
        msgBubbleColorBot: '#444',
        msgBubbleBgUser: '#007ABD',
        msgBubbleColorUser: '#E3F5FF',
        inputPlaceholder: 'Type a message here',
        inputDisableBg: '#fff',
        inputDisablePlaceholder: null
      }
    }
  },

  methods: {
    botStart () {
      // Get token if you want to build a private bot
      // Request first message here

      // Fake typing for the first message
      this.botTyping = true
      setTimeout(() => {
        this.botTyping = false
        this.messageData.push({
          agent: 'bot',
          type: 'text',
          text: 'Hello'
        })
      }, 1000)
    },

    msgSend (value) {
      // Push the user's message to board
      this.messageData.push({
        agent: 'user',
        type: 'text',
        text: value.text
      })

      this.getResponse()
    },

    // Submit the message from user to bot API, then get the response from Bot
    getResponse () {
      // Loading
      this.botTyping = true

      // Post the message from user here
      // Then get the response as below

      // Create new message from fake data
      messageService.createMessage()
        .then((response) => {
          const replyMessage = {
            agent: 'bot',
            ...response
          }

          this.inputDisable = response.disableInput
          this.messageData.push(replyMessage)

          // finish
          this.botTyping = false
        })
    }
  }
}
</script>
<style lang="scss">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  // text-align: center;
  color: #2c3e50;
}
</style>
