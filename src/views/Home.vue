<template>
  <div class="home">
    <h4>jobs</h4>
    <div>
      <p v-for="job in jobs" v-bind:key="job.id">{{ job }}</p>
    </div>
  </div>
</template>

<script>
import axios from "axios";
// @ is an alias to /src

export default {
  name: "Home",
  data: function () {
    return {
      jobs: [],
    };
  },
  created: function () {
    this.indexJobs();
    // this.indexWWR();
  },
  components: {},
  methods: {
    indexWWR: function () {
      axios.get("/wwr").then((response) => {
        console.log("wwr", response);
        this.jobs.push(response.data[16]);
      });
    },
    indexJobs: function () {
      axios.get("/jobs").then((response) => {
        console.log("jobs", response);
        this.jobs = response.data;
      });
    },
    beforeMount() {
      this.indexJobs();
      // this.indexWWR();
    },
  },
};
</script>

<style scoped>
h1 {
  padding: 1em;
}
</style>
