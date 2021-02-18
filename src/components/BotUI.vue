<template lang="pug">
<div>
    .qkb-board-page(v-if="!botActive")
      BoardContent(
        :bot-typing="botTyping",
        :main-data="messages"
      )
      BoardAction(
        :input-disable="inputDisable",
        :input-placeholder="optionsMain.inputPlaceholder",
        :input-disable-placeholder="optionsMain.inputDisablePlaceholder",
        @msg-send="sendMessage"
      )
.qkb-bot-ui(
  :class="uiClasses"
)
  transition(name="qkb-fadeUp")
    .qkb-board(v-if="botActive")
      BoardHeader(
        :bot-title="optionsMain.botTitle",
        v-bind:close-bot="botToggle"
      )
      BoardContent(
        :bot-typing="botTyping",
        :main-data="messages"
      )
      BoardAction(
        :input-disable="inputDisable",
        :input-placeholder="optionsMain.inputPlaceholder",
        :input-disable-placeholder="optionsMain.inputDisablePlaceholder",
        @msg-send="sendMessage"
      )

    button.qkb-bubble-btn(
      @click="botToggle"
    )
      slot(name="bubbleButton")
        transition(name="qkb-scaleUp")
          BubbleIcon.qkb-bubble-btn-icon(
            v-if="!botActive",
            key="1"
          )
          CloseIcon.qkb-bubble-btn-icon.qkb-bubble-btn-icon--close(
            v-else,
            key="2"
          )
  AppStyle(:options="optionsMain")
  .qkb-preload-image
    .qkb-msg-avatar__img(v-if="optionsMain.botAvatarImg")
</div>
</template>
<script>
import EventBus from '../helpers/event-bus'
import BoardHeader from './Board/Header'
import BoardContent from './Board/Content'
import BoardAction from './Board/Action'
import AppStyle from './AppStyle'
import BubbleIcon from '../assets/icons/bot-icon.svg'
import CloseIcon from '../assets/icons/close.svg'
export default {
  name: 'VueBotUI',

  components: {
    BoardHeader,
    BoardContent,
    BoardAction,
    BubbleIcon,
    CloseIcon,
    AppStyle
  },

  props: {
    options: {
      type: Object,
      default: () => { return {} }
    },

    messages: {
      type: Array
    },

    botTyping: {
      type: Boolean,
      default: false
    },

    inputDisable: {
      type: Boolean,
      default: false
    },

    isOpen: {
      type: Boolean,
      default: false
    },

    openDelay: {
      type: Number
    }
  },

  data () {
    return {
      botActive: false,
      defaultOptions: {
        botTitle: 'My bot',
        colorScheme: '#1b53d0',
        textColor: '#fff',
        bubbleBtnSize: 56,
        animation: true,
        boardContentBg: '#fff',
        botAvatarSize: 32,
        botAvatarImg: 'http://placehold.it/200x200',
        msgBubbleBgBot: '#f0f0f0',
        msgBubbleColorBot: '#000',
        msgBubbleBgUser: '#4356e0',
        msgBubbleColorUser: '#fff',
        inputPlaceholder: 'Type a message here',
        inputDisableBg: '#fff',
        inputDisablePlaceholder: null
      }
    }
  },

  computed: {
    optionsMain () {
      return { ...this.defaultOptions, ...this.options }
    },

    // Add class to bot ui wrapper
    uiClasses () {
      let classes = []

      if (this.optionsMain.animation) {
        classes.push('qkb-bot-ui--animate')
      }

      return classes
    }
  },

  created () {
    window.addEventListener('scroll', this.handleScroll)
    if (this.isOpen) {
      if (this.openDelay) {
        setTimeout(this.botOpen, this.openDelay)
      } else {
        this.botToggle()
      }
    }
  },
  mounted () {
    if (this.checkVisible(document.getElementsByClassName('qkb-board-content')[0])) {
      document.getElementsByClassName('qkb-bubble-btn')[0].style.display = 'none'
    }
  },
  beforeDestroy () {
    EventBus.$off('select-button-option')
  },

  methods: {
    botOpen () {
      if (!this.botActive) {
        this.botToggle()
      }
    },
    handleScroll (event) {
      if (this.checkVisible(document.getElementsByClassName('qkb-board-content')[0])) {
        document.getElementsByClassName('qkb-bubble-btn')[0].style.display = 'none'
      } else {
        document.getElementsByClassName('qkb-bubble-btn')[0].style.display = 'block'
      }
    },
    checkVisible (elm) {
      var rect = elm.getBoundingClientRect()
      var viewHeight = Math.max(document.documentElement.clientHeight, window.innerHeight)
      return !(rect.bottom < 0 || rect.top - viewHeight >= 0)
    },
    botToggle () {
      this.botActive = !this.botActive

      if (this.botActive) {
        document.body.style.overflow = 'hidden'
        EventBus.$on('select-button-option', this.selectOption)
        this.$emit('init')
      } else {
        document.body.style.overflow = 'scroll'
        EventBus.$off('select-button-option')
        this.$emit('destroy')
      }
    },

    sendMessage (value) {
      this.$emit('msg-send', value)
    },

    selectOption (value) {
      this.$emit('msg-send', value)
    }
  }
}
</script>

<style src="../assets/scss/_app.scss" lang="scss">

</style>
