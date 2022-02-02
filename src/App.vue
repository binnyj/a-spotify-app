<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <HelloWorld msg="Welcome to Your Vue.js App"/>
  <p>Joseph was here</p>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";

export default {
  name: "App",
  components: {
    HelloWorld,
  },
  data() {
    return {
      access_token: "",
      refresh_token: "",
    };
  },
  created() {
    if (window.location.href.includes("code=")) {
      let code = null;
      const queryString = window.location.search;
      if (queryString) {
        const urlParams = new URLSearchParams(queryString);
        code = urlParams.get("code");
        this.requestAccessToken(code).then((data) => {
          this.access_token = data.access_token;
          this.refresh_token = data.refresh_token;
        });
      }
    } else {
      this.authorizeSpotify();
    }
  },
  methods: {
    async authorizeSpotify() {
      // const state = Math.random().toString(16).substr(2, 8);
      const scope = "user-read-private user-read-email";

      window.location.href =
        "https://accounts.spotify.com/authorize?" +
        "response_type=code&" +
        `client_id=${process.env.VUE_APP_CLIENT_ID}&` +
        `scope=${scope}&` +
        `redirect_uri=${encodeURI(process.env.VUE_APP_REDIRECT_URL)}&` +
        `show_dialog=true`;
    },
    async requestAccessToken(code) {
      const authKey = Buffer.from(
        process.env.VUE_APP_CLIENT_ID + ":" + process.env.VUE_APP_CLIENT_SEC
      ).toString("base64");

      const response = await fetch("https://accounts.spotify.com/api/token", {
        method: "POST",
        headers: {
          Authorization: `Basic ${authKey}`,
          "Content-Type": "application/x-www-form-urlencoded",
        },
        body: new URLSearchParams({
          grant_type: "authorization_code",
          code,
          redirect_uri: process.env.VUE_APP_REDIRECT_URL,
        }).toString(),
      });

      return response.json();
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
