<template>
  <div id="app">
    <nav class="flex items-center justify-between flex-wrap bg-teal-500 p-6">
      <div class="w-full block flex-grow flex items-center w-auto">
        <div class="text-sm flex-grow">
          <router-link
            class="block mt-4 inline-block mt-0 text-teal-200 hover:text-white mr-4"
            to="/"
            >Home</router-link
          >
          <router-link
            class="block mt-4 inline-block mt-0 text-teal-200 hover:text-white mr-4"
            v-if="!isLoggedIn"
            to="/login"
            >Login</router-link
          >
          <a
            v-if="isLoggedIn"
            class="block mt-4 inline-block mt-0 text-teal-200 hover:text-white cursor-pointer"
            @click="logout"
            >Logout</a
          >
        </div>
      </div>
    </nav>
    <router-view />
  </div>
</template>
<script>
export default {
  computed: {
    isLoggedIn() {
      return this.$store.getters.isLoggedIn;
    }
  },
  methods: {
    logout() {
      this.$store.dispatch("logout").then(() => {
        this.$router.push("/login");
      });
    }
  },
  created() {
    this.$http.interceptors.response.use(undefined, err => {
      return new Promise((resolve, reject) => {
        if (err.status === 401 && err.config && !err.config.__isRetryRequest) {
          this.$store.dispatch(logout);
        }
        throw err;
      });
    });
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
  cursor: pointer;
}

#nav a:hover {
  text-decoration: underline;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
</style>
