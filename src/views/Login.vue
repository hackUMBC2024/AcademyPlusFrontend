<template>
  <div class="login-container">
    <form @submit.prevent="handleLogin">
      <h1 id="signin">Sign in</h1>
      <div class="form-group">
        <label for="username">Username</label>
        <input
          type="text"
          id="username"
          v-model="username"
          required
          autocomplete="off"
        />
      </div>
      <div class="form-group">
        <label for="password">Password</label>
        <input
          type="password"
          id="password"
          v-model="password"
          required
          autocomplete="off"
        />
      </div>
      <div>

      </div>
      <button type="submit" @click="handleLogin">Login</button>
    </form>
  </div>
</template>

<script>
import { mapStores } from "pinia";
import { useGlobalStore } from "@/stores/store";

export default {
  data() {
    return {
      username: '',
      password: ''
    }
  },
  methods: {
    async handleLogin() {
      let fetchRespone = await fetch("/api/login", {
        method: "POST",
        headers: {
          Accept: "application/json",
          "Content-Type": "application/json"
        },
        body: JSON.stringify({username: this.username, password: this.password})
      });

      let response = await fetchRespone.json();

      if(response.error) {
        console.log("Error code: " + response.error);
        console.log("Error content: " + response.content);

        if(response.content == "Already logged in") {
          this.$router.push({ path: "/search"});
        }
        //Handle Error here figure it out
        return;
      }

      
      console.log("Success code:" + response.success);
      console.log("Success content: " + response.content);

      console.log(response);

      let global = useGlobalStore();
      global.username = response.username;
      global.isLoggedIn = true;

      let userFetchRespone = await fetch("/api/userdata");
      let responseSecond = await userFetchRespone.json();
      
      console.log(responseSecond);

      if(responseSecond.error) {
        console.log("Error code: " + response.error);
        console.log("Error content: " + response.content);
        //Handle Error here figure it out 
        return;
      }

      global.username = responseSecond.username;

      this.$router.push({ path: "/search"});
    },
  },
  computed: {
    ...mapStores(useGlobalStore)
  }
}
</script>

<style scoped>
.login-container {
  display: flex;
  justify-content: center;
  align-items: center;
}

#signin {
  text-align: left;
  justify-content: left;
}

form {
  background: var(--color-background-soft);
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  width: 20rem;
}

h1 {
  color: var(--color-heading);
  margin-bottom: 20px;
  text-align: center;
}

.form-group {
  margin-bottom: 15px;
}

label {
  display: block;
  margin-bottom: 5px;
  color: var(--color-text);
}

input {
  width: 100%;
  padding: 8px;
  border: 1px solid var(--color-border);
  border-radius: 4px;
  box-sizing: border-box;
  transition: border-color 0.3s;
}

input:focus {
  border-color: var(--color-border-hover);
}

button {
  padding:1rem;
  width: 100%;
  background-color: var(--color-accent);
  color: var(--color-text);
  border: none;
  border-radius: 0.1rem;
  cursor: pointer;
  transition: background-color 0.3s;
}

button:hover {
  background-color: var(--color-accent-dark);
}
</style>
