<template>
  <div
    class="sc-wrapper"
    :class="{
      opened: isOpen && !embedded,
      'sc-wrapper--with-actions': additionalActions.length !== 0,
      embedded,
    }"
  >
    <div v-if="!embedded" class="sc-launcher" :class="{opened: isOpen}" :style="{backgroundColor: colors.launcher.bg}">
      <div v-if="newMessagesCount > 0 && !isOpen" class="sc-new-messsages-count">
        {{newMessagesCount}}
      </div>
      <div
        v-for="(action, idx) in additionalActions"
        :key="idx"
        :class="`sc-additional-action ${action.class || ''}`"
        :style="{
          transitionDelay: `${50 * idx}ms`,
          transform: `translateX(-${isOpen ? 65 + (40 + 15) * idx : 0}px)`
        }"
        @click="action.onClick"
      />
      <img @click.prevent="isOpen ? close() : open()" class="sc-open-icon" src="./assets/close-icon.png" />
      <img @click.prevent="isOpen ? close() : open()" class="sc-closed-icon" src="./assets/logo-no-bg.svg" />
    </div>
    <ChatWindow
      :messageList="messageList"
      :onUserInputSubmit="onMessageWasSent"
      :participants="participants"
      :title="chatWindowTitle"
      :titleImageUrl="titleImageUrl"
      :isOpen="isOpen || embedded"
      :onClose="close"
      :showEmoji="showEmoji"
      :showFile="showFile"
      :placeholder="placeholder"
      :showTypingIndicator="showTypingIndicator"
      :colors="colors"
      :alwaysScrollToBottom="alwaysScrollToBottom"
      :messageStyling="messageStyling"
      :embedded="embedded"
      @scrollToTop="$emit('scrollToTop')"
    />
    <div
      v-if="embedded && additionalActions.length !== 0"
      :style="{
        textAlign: 'right',
        height: '24px',
        margin: '6px auto',
      }"
    >
      <div
        v-for="(action, idx) in additionalActions"
        :key="idx"
        :class="`sc-additional-action ${action.class || ''}`"
        @click="action.onClick"
      />
    </div>
  </div>
</template>
<script>
import ChatWindow from './ChatWindow.vue'

export default {
  props: {
    showEmoji: {
      type: Boolean,
      default: false
    },
    isOpen: {
      type: Boolean,
      required: true
    },
    open: {
      type: Function,
      required: true
    },
    close: {
      type: Function,
      required: true
    },
    showFile: {
      type: Boolean,
      default: false
    },
    participants: {
      type: Array,
      required: true
    },
    title: {
      type: String,
      default: () => ''
    },
    titleImageUrl: {
      type: String,
      default: () => ''
    },
    onMessageWasSent: {
      type: Function,
      required: true
    },
    messageList: {
      type: Array,
      default: () => []
    },
    newMessagesCount: {
      type: Number,
      default: () => 0
    },
    placeholder: {
      type: String,
      default: 'Write a reply'
    },
    showTypingIndicator: {
      type: String,
      default: () => ''
    },
    colors: {
      type: Object,
      required: false,
      validator: c => 
        'header' in c
        && 'bg' in c.header && 'text' in c.header
        && 'launcher' in c
        && 'bg' in c.launcher
        && 'messageList' in c
        && 'bg' in c.messageList
        && 'sentMessage' in c
        && 'bg' in c.sentMessage && 'text' in c.sentMessage
        && 'receivedMessage' in c
        && 'bg' in c.receivedMessage && 'text' in c.receivedMessage
        && 'userInput' in c
        && 'bg' in c.userInput && 'text' in c.userInput,
      default: function () {
        return {
          header: {
            bg: '#4e8cff',
            text: '#ffffff'
          },
          launcher: {
            bg: '#4e8cff'
          },
          messageList: {
            bg: '#ffffff'
          },
          sentMessage: {
            bg: '#4e8cff',
            text: '#ffffff'
          },
          receivedMessage: {
            bg: '#f4f7f9',
            text: '#ffffff'
          },
          userInput: {
            bg: '#f4f7f9',
            text: '#565867'
          }
        }
      }
    },
    alwaysScrollToBottom: {
      type: Boolean,
      default: () => false
    },
    messageStyling: {
      type: Boolean,
      default: () => false
    },
    additionalActions: {
      type: Array,
      default: () => [],
    },
    embedded: {
      type: Boolean,
      default: false,
    },
  },
  computed: {
    chatWindowTitle() {
      if (this.title !== '') {
        return this.title
      }

      if (this.participants.length > 1) {
        return 'You, ' + this.participants[0].name + ' & others'
      } else {
        return 'You & ' + this.participants[0].name
      }
    }
  },
  components: {
    ChatWindow
  }
}
</script>
<style scoped>
.sc-wrapper {
  position: fixed;
  right: 90px;
  bottom: 75px;
}

.sc-wrapper.embedded {
  position: relative;
  right: auto;
  bottom: auto;
  width: 100%;
  height: 100%;
  box-sizing: border-box;
}

.sc-wrapper.embedded.sc-wrapper--with-actions {
  padding-bottom: 36px;
}

.sc-wrapper.embedded .sc-additional-action {
  display: inline-block;
  width: 24px;
  height: 24px;
  border-radius: 24px;
}

.sc-wrapper.embedded .sc-additional-action:not(:last-of-type) {
  margin-right: 5px;
}

.sc-launcher {
  position: absolute;
  top: 0;
  left: 0;
  width: 60px;
  height: 60px;
  background-position: center;
  background-repeat: no-repeat;
  border-radius: 50%;
  box-shadow: none;
  transition: box-shadow 0.2s ease-in-out;
  cursor: pointer;
}

.sc-launcher:before {
  content: '';
  position: relative;
  display: block;
  width: 60px;
  height: 60px;  
  border-radius: 50%;
  transition: box-shadow 0.2s ease-in-out;
}

.sc-launcher .sc-open-icon,
.sc-launcher .sc-closed-icon {
  position: absolute;
  left: 0;
  top: 0;
  width: 60px;
  height: 60px;
  transition: opacity 100ms ease-in-out, transform 100ms ease-in-out;
}

.sc-launcher .sc-additional-action {
  position: absolute;
  width: 40px;
  height: 40px;
  top: 10px;
  left: 10px;
  border-radius: 40px;
  opacity: 0;
  transition: opacity 250ms ease-in-out, transform 200ms ease-in-out;
}

.sc-launcher .sc-closed-icon {
  transition: opacity 100ms ease-in-out, transform 100ms ease-in-out;
  width: 60px;
  height: 60px;
}

.sc-launcher .sc-open-icon {
  padding: 20px;
  box-sizing: border-box;
  opacity: 0;
}

.sc-launcher.opened .sc-open-icon {
  transform: rotate(-90deg);
  opacity: 1;
}

.sc-launcher.opened .sc-closed-icon {
  transform: rotate(-90deg);
  opacity: 0;
}

.sc-launcher.opened .sc-additional-action {
  opacity: 1;
}

.sc-launcher.opened:before {
  box-shadow: 0px 0px 400px 250px rgba(148, 149, 150, 0.2);
}

.sc-launcher:hover {
  box-shadow: 0 0px 27px 1.5px rgba(0,0,0,0.2);
}

.sc-new-messsages-count {
  position: absolute;
  top: -3px;
  left: 41px;
  display: flex;
  justify-content: center;
  flex-direction: column;
  border-radius: 50%;
	width: 22px;
  height: 22px;
  background: #ff4646;
  color: white;
  text-align: center;
  margin: auto;
  font-size: 12px;
  font-weight: 500;
}

@media (max-width: 450px) {
  .sc-wrapper.opened {
    position: fixed;
    top: 0;
    right: 0;
    bottom: auto;
    height: 100%;
    width: 100%;
  }
  .sc-launcher.opened {
    top: auto;
    left: auto;
    bottom: 5px;
    right: 5px;
  }
}
</style>
