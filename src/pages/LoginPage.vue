<template>
  <div class="login-page">
    <h1>Login</h1>
    <form @submit.prevent="login">
      <div class="form-group">
        <label>Username:</label>
        <input v-model="state.username" type="text" class="form-control" />
        <div v-if="v$.username.$error" class="text-danger">
          Username is required.
        </div>
      </div>
      <div class="form-group">
        <label>Password:</label>
        <input v-model="state.password" type="password" class="form-control" />
        <div v-if="v$.password.$error" class="text-danger">
          Password is required .
        </div>
      </div>
      <button type="submit" class="btn btn-primary mt-3">Login</button>
    </form>
  </div>
</template>

<script>
import { reactive } from 'vue';
import { useVuelidate } from '@vuelidate/core';
import { required } from '@vuelidate/validators';

export default {
  name: "LoginPage",
  setup(_, { expose }) {
    const state = reactive({
      username: '',
      password: '',
    });

    const rules = {
      username: { required },
      password: { required },
    };

    const v$ = useVuelidate(rules, state);

    const login = async () => {
      if (await v$.value.$validate()) {
        // קריאה לשרת
        try {
          await window.axios.post(`${window.store.server_domain}/api/login`, {
            username: state.username,
            password: state.password
          });
          console.log("Logging in with:", state.username, state.password);
          window.store.login(state.username);
          window.router.push('/');
        } catch (err) {
            const message = err.response?.data?.message || 'Login failed. Please check your username and password.';
            window.toast('Login Failed', message, 'danger');
        }
      }
    };

    expose({ login });

    return { state, v$, login };
  }
};
</script>

<style scoped>
.login-page {
  max-width: 400px;
  margin: auto;
}
</style>
