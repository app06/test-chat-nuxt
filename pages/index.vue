<template>
  <div id="app">
    <WelcomeForm @login="login" v-if="!user.id" />
    <b-container v-else class="app-container">
      <Chat :user="user" />
    </b-container>
  </div>
</template>

<script>
import WelcomeForm from '~/components/WelcomeForm.vue';
import Chat from '~/components/Chat.vue';

export default {
  name: 'app',
  data: () => ({
    user: {
      id: null,
      nickname: null
    }
  }),
  components: {
    WelcomeForm,
    Chat
  },
  methods: {
    login(user) {
      this.user = user;
    }
  }
};
</script>

<style>
.app-container {
  padding-top: 50px;
  padding-bottom: 50px;
}
</style>
