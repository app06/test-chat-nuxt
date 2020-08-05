<template>
  <b-row>
    <b-col>
      <b-card class="dialog">
        <div class="dialog-messages" ref="dialogMessages">
          <div
            v-for="message in messages"
            :key="message.id"
            :class="[message.userId === user.id ? 'owner' : '', 'message']"
          >
            <div class="message-nickname" v-if="message.userId !== user.id">
              {{ message.userNickname }}
            </div>
            <div class="message-text">
              {{ message.text }}
            </div>
          </div>
        </div>
        <div class="dialog-form">
          <form @submit.prevent="sendMessage">
            <b-form-textarea
              v-model.trim="message"
              @keyup.enter.exact="sendMessage"
            ></b-form-textarea>
            <button type="submit">
              <b-icon-arrow-right-circle></b-icon-arrow-right-circle>
            </button>
          </form>
        </div>
      </b-card>
    </b-col>
  </b-row>
</template>

<script>
import axios from 'axios';

import '~/assets/scss/custom.scss';

const apiUrl = process.env.apiUrl;

export default {
  name: 'Chat',
  props: {
    user: {
      required: true,
      type: Object
    }
  },
  data: () => ({
    canSend: true,
    messages: [],
    message: ''
  }),
  created() {
    this.loadMessages();

    this.refreshMessages();
  },
  computed: {
    isBrowser() {
      return process.client;
    }
  },
  methods: {
    loadMessages() {
      axios
        .get(apiUrl)
        .then(messages => {
          this.messages = messages.data;
          this.scrollMessages();
        })
        .catch(error => console.log(error));
    },
    refreshMessages() {
      setInterval(this.loadMessages, 1000);
    },
    sendMessage() {
      if (!this.message || !this.canSend) return;

      this.canSend = false;

      const message = {
        id: new Date().getTime(),
        text: this.message,
        userNickname: this.user.nickname,
        userId: this.user.id
      };

      this.message = '';
      this.messages.push(message);

      this.scrollMessages();

      this.saveMessage(message);
    },
    saveMessage(message) {
      axios
        .post(apiUrl, message)
        .then(() => (this.canSend = true))
        .catch(error => console.log(error));
    },
    scrollMessages() {
      if (!this.isBrowser) return;

      this.$nextTick(function() {
        const el = this.$refs.dialogMessages;
        el.scrollTop = el.scrollHeight;
      });
    }
  }
};
</script>

<style>
.dialog {
  height: calc(100vh - 100px);
  min-height: 175px;
  padding-bottom: 75px;
}

.dialog textarea.form-control {
  resize: none;
  height: 38px;
  padding-right: 30px;
  overflow: hidden;
  outline: none;
}

.dialog textarea.form-control:focus {
  border-color: #ced4da;
  box-shadow: none;
}

.dialog-form {
  position: absolute;
  bottom: 1.25rem;
  left: 1.25rem;
  right: 1.25rem;
}

.dialog-form button {
  position: absolute;
  border: none;
  background: transparent;
  padding: 0;
  font-size: 25px;
  color: #ced4da;
  right: 5px;
  top: -3px;
  outline: none;
  transition: color 0.3s ease-in-out;
}

.dialog-form button:hover {
  color: #000;
}

.dialog-form button:focus {
  outline: none;
}

.dialog-messages {
  position: absolute;
  top: 1.25rem;
  left: 1.25rem;
  right: 1.25rem;
  bottom: 75px;
  overflow-y: auto;
  overflow-x: hidden;
}

.message {
  margin-bottom: 10px;
  text-align: left;
}

.message-text {
  display: inline-block;
  background-color: #d1ecf1;
  border-radius: 0.25rem;
  padding: 0.2rem 0.4rem;
  margin-right: 5%;
  max-width: 95%;
}

.message.owner .message-text {
  background-color: #f8f9fa;
  margin-right: 0;
  margin-left: 5%;
}

.message.owner {
  text-align: right;
}

.message-nickname {
  font-size: 10px;
}

</style>
