<template>
  <div class="home">
    <h1>{{ message }}</h1>
  </div>
</template>

<style>
</style>

<script>
import axios from "axios";
export default {
  data: function() {
    return {
      message: "Welcome to Vue.js!",
    };
  },

  created: function() {
    this.findLastPushed()
  },

  methods: {
    findLastPushed: async function() {
      const url = "https://api.github.com/users/yitzy32/repos?per_page=100";

      await axios.get(url)
      .then((response) => {
        let lastPushedMs = this.convertToMs(response.data[0].pushed_at),
            repoName = "";
        for (const repo of response.data) {
          let pushedAtMs = this.convertToMs(repo.pushed_at);
          if (pushedAtMs > lastPushedMs) {
            lastPushedMs = pushedAtMs;
            repoName = repo.name;
          }
        }
          console.log("The last pushed:", "Repo Name: ", repoName, "lastPushedMs:",lastPushedMs)
      });
    },

    convertToMs: function(date) {
      return new Date(date).getTime()
    }
  }
};
</script>