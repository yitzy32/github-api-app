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
      const headers = {
        "Authorization": `Token ${process.env.VUE_APP_PERSONAL_ACCESS_TOKEN}`
      }
      let repoName = "",
          owner = "";

      await axios.get(url, {
        "method": "GET",
        "headers": headers 
      })
      .then((response) => {
        let lastPushedMs = this.convertToMs(response.data[0].pushed_at);
        for (const repo of response.data) {
          let pushedAtMs = this.convertToMs(repo.pushed_at);
          if (pushedAtMs > lastPushedMs) {
            lastPushedMs = pushedAtMs;
            repoName = repo.name;
            owner = repo.owner.login;
          }
        }
        let lastPushed = this.convertToLegibleDate(lastPushedMs)
          console.log("The last pushed:", "Repo Name: ", repoName, "lastPushed:",lastPushed, "Owner:", owner)
      });
      await axios.get(`https://api.github.com/repos/yitzy32/${repoName}/languages`).then((response) => {
        console.log(Object.keys(response.data))
      })
    },
    // findLanguages: function (repoName) {
    //   language_tracker = {}
    //   axios.get(`https://api.github.com/repos/yitzy32/${repoName}/languages`).then((response) => {
    //     let object = response.data;
    //     for (const lang in object) {
    //       if (language_tracker[lang]) {
    //         language_tracker[lang] += 1
    //       } else {
    //         language_tracker[lang] = 1
    //       }
    //     }
    //     // console.log(language_tracker)
    //     return language_tracker
    //   })
    // },
    convertToMs: function(date) {
      return new Date(date).getTime()
    },
    convertToLegibleDate: function(date) {
      return new Date(date).toString()
    }
  }
};
</script>