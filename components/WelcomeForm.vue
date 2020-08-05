<template>
  <div class="welcome">
    <b-card header-tag="header" title="Вход">
      <form @submit.prevent="login">
        <b-form-group id="fieldset-1" label-for="input-1">
          <b-form-input
            id="input-1"
            v-model="user.nickname"
            trim
            required
            placeholder="Никнейм"
          ></b-form-input>
        </b-form-group>
        <b-button type="submit" variant="primary">Войти</b-button>
      </form>
    </b-card>
  </div>
</template>

<script>
export default {
  name: 'WelcomeForm',
  data: () => ({
    user: {
      id: null,
      nickname: ''
    }
  }),
  methods: {
    login() {
      this.user.id = new Date().getTime();
      this.$emit('login', this.user);
    }
  }
};
</script>

<style>
.welcome {
  padding-top: 50px;
  width: 300px;
  margin-left: auto;
  margin-right: auto;
}
</style>
