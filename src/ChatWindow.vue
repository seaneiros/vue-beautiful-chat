<template>
  <div class="sc-chat-window" :class="{opened: isOpen, closed: !isOpen, embedded}">
    <Header
      v-if="!embedded"
      :title="title"
      :imageUrl="titleImageUrl"
      :onClose="onClose"
      :colors="colors"
      :disableUserListToggle="disableUserListToggle"
      @userList="handleUserListToggle"
    />
    <UserList 
      v-if="showUserList && !embedded"
      :participants="participants"
    />
    <MessageList
      v-if="!showUserList"
      :messages="messages"
      :participants="participants"
      :showTypingIndicator="showTypingIndicator"
      :colors="colors"
      :alwaysScrollToBottom="alwaysScrollToBottom"
      :messageStyling="messageStyling"
      @scrollToTop="$emit('scrollToTop')"
    />
    <UserInput
      v-if="!showUserList"
      :embedded="embedded"
      :showEmoji="showEmoji"
      :onSubmit="onUserInputSubmit"
      :suggestions="getSuggestions()"
      :showFile="showFile"
      :placeholder="placeholder"
      :colors="colors" />
  </div>
</template>

<script>
import Header from './Header.vue'
import MessageList from './MessageList.vue'
import UserInput from './UserInput.vue'
import UserList from './UserList.vue'

export default {
  components: {
    Header,
    MessageList,
    UserInput,
    UserList
  },
  props: {
    embedded: {
      type: Boolean,
      defafult: false,
    },
    showEmoji: {
      type: Boolean,
      default: false
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
      required: true
    },
    titleImageUrl: {
      type: String,
      default: ''
    },
    onUserInputSubmit: {
      type: Function,
      required: true
    },
    onClose: {
      type: Function,
      required: true
    },
    messageList: {
      type: Array,
      default: () => []
    },
    isOpen: {
      type: Boolean,
      default: () => false
    },
    placeholder: {
      type: String,
      default: 'Write a reply'
    },
    showTypingIndicator: {
      type: String,
      required: true
    },
    colors: {
      type: Object,
      required: true
    },
    alwaysScrollToBottom: {
      type: Boolean,
      required: true
    },
    messageStyling: {
      type: Boolean,
      required: true
    },
    disableUserListToggle: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      showUserList: false
    }
  },
  computed: {
    messages() {
      let messages = this.messageList

      return messages
    }
  },
  methods: {
    handleUserListToggle(showUserList) {
      this.showUserList = showUserList
    },
    getSuggestions(){
      return this.messages.length > 0 ? this.messages[this.messages.length - 1].suggestions : []
    }
  }
}
</script>
<style scoped>
@keyframes fade-in {
  0% {
    opacity: 0;
    visibility: hidden;
    transform: translateX(calc(60px - 100%)) translateY(calc(-100%));
  }
  90% {
    visibility: visible;
  }
  100% {
    opacity: 1;
    transform: translateX(calc(60px - 100%)) translateY(calc(-20px - 100%));
  }
}

.sc-chat-window {
  position: absolute;
  left: 0;
  top: 0;
  width: 370px;
  max-height: 590px;
  box-sizing: border-box;
  box-shadow: 0px 7px 40px 2px rgba(148, 149, 150, 0.1);
  background: white;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  border-radius: 10px;
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
}

.sc-chat-window.opened {
  animation: fade-in .3s ease-in-out 0s 1 normal both;
}
.sc-chat-window.closed {
  opacity: 0;
  visibility: hidden;
  transform: translateX(calc(60px - 100%)) translateY(60px);
}

.sc-chat-window.embedded {
  position: relative;
  width: 100%;
  height: 100%;
  max-height: none;
  box-shadow: none;
  animation: none;
  border-radius: 0;
}

.sc-message--me {
  text-align: right;
}
.sc-message--them {
  text-align: left;
}

@media (max-width: 450px) {
  .sc-chat-window {
    width: 100%;
    height: calc(100% - 70px);
    max-height: 100%;
    right: 0px;
    bottom: 0px;
    border-radius: 0px;
    animation: none;
    transition: 0.2s ease-in-out;
  }
  .sc-chat-window.opened {
    opacity: 1;
    animation: none;
    transform: translateX(0) translateY(0);
  }
  .sc-chat-window.closed {
    opacity: 0;
    transform: translateX(0) translateY(calc(80px + 100%));
  }
}
</style>
