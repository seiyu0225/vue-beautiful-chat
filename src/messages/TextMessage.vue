<template>
  <div class="sc-message--text" :style="messageColors">
    <template> </template>
    <slot :message="message" :messageText="messageText" :messageColors="messageColors" :me="me">
      <p class="sc-message--text-content" v-html="messageText"></p>
    </slot>
  </div>
</template>

<script>
import {mapState} from '../store/'
import escapeGoat from 'escape-goat'
import Autolinker from 'autolinker'
import store from '../store/'

const fmt = require('msgdown')

export default {
  components: {},
  props: {
    message: {
      type: Object,
      required: true
    },
    messageColors: {
      type: Object,
      required: true
    },
    messageStyling: {
      type: Boolean,
      required: true
    }
  },
  computed: {
    messageText() {
      const escaped = escapeGoat.escape(this.message.data.text)

      return Autolinker.link(this.messageStyling ? fmt(escaped) : escaped, {
        className: 'chatLink',
        truncate: {length: 50, location: 'smart'}
      })
    },
    me() {
      return this.message.author === 'me'
    },
    isEditing() {
      return (store.state.editMessage && store.state.editMessage.id) === this.message.id
    },
    ...mapState(['showDeletion', 'showEdition'])
  },
  methods: {
    edit() {
      store.setState('editMessage', this.message)
    }
  }
}
</script>

<style scoped lang="scss">
.sc-message--text {
  padding: 5px 20px;
  border-radius: 6px;
  font-weight: 300;
  font-size: 14px;
  line-height: 1.4;
  position: relative;
  -webkit-font-smoothing: subpixel-antialiased;
  .sc-message--text-content {
    white-space: pre-wrap;
  }
  &:hover .sc-message--toolbox {
    left: -20px;
    opacity: 1;
  }
  .sc-message--toolbox {
    transition: left 0.2s ease-out 0s;
    white-space: normal;
    opacity: 0;
    position: absolute;
    left: 0px;
    width: 25px;
    top: 0;
    button {
      background: none;
      border: none;
      padding: 0px;
      margin: 0px;
      outline: none;
      width: 100%;
      text-align: center;
      cursor: pointer;
      &:focus {
        outline: none;
      }
    }
    & /deep/ svg {
      margin-left: 5px;
    }
  }
  code {
    font-family: 'Courier New', Courier, monospace !important;
  }
}

.sc-message--content.sent .sc-message--text {
  color: white;
  background-color: #4e8cff;
  max-width: calc(100% - 120px);
  word-wrap: break-word;
}

.sc-message--content.received .sc-message--text {
  color: #263238;
  background-color: #f4f7f9;
  margin-right: 40px;
}

a.chatLink {
  color: inherit !important;
}
</style>
