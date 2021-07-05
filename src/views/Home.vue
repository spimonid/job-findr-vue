<template>
  <div class="home">
    <h4>jobs</h4>
    <div class="container mt-4">
      <table class="table table-bordered">
        <thead>
          <tr>
            <th scope="col">Company ID</th>
            <th scope="col">Role</th>
            <th scope="col">Remote</th>
            <th scope="col">Technologies</th>
            <th scope="col">Save Job?</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="job in jobs" v-bind:key="job.id">
            <td>{{ job.company_id }}</td>
            <td>{{ job.role }}</td>
            <td>{{ job.remote }}</td>
            <td>{{ job.technologies }}</td>
            <td><button type="button" v-on:click="saveJob(job)">save job</button></td>
          </tr>
        </tbody>
      </table>
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
    saveJob: function (job) {
      var params = {
        job_id: job.id,
      };
      axios
        .post("/saved_jobs/", params)
        .then((response) => {
          console.log(response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
          console.log(this.errors);
        });
      console.log(job);
    },
    beforeMount() {
      this.indexJobs();
      // this.indexWWR();
    },
  },
};
</script>

<style scoped>
h4 {
  padding: 1em;
}
</style>
