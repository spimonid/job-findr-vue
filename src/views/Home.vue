<template>
  <div class="home">
    <h4>jobs</h4>
    <div class="container mt-4">
      <table class="highlight">
        <thead>
          <tr>
            <th scope="col">Company</th>
            <th scope="col">Role</th>
            <th scope="col">Remote</th>
            <th scope="col">Technologies</th>
            <th scope="col">Save Job?</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="job in jobs" v-bind:key="job.id">
            <td>{{ job.company.name }}</td>
            <td>{{ job.role }}</td>
            <td>{{ job.remote }}</td>
            <td>{{ job.technologies.split(" ")[0] }}</td>
            <td>
              <div v-if="isLoggedIn()">
                <button type="button" v-if="!isSaved(job)" v-on:click="saveJob(job)">save job</button>
                <span v-else>saved!</span>
              </div>
            </td>
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
      wwr_jobs: [],
      savedJobsIds: [],
    };
  },
  created: function () {
    this.indexJobs();
    this.indexSavedJobs();
  },
  components: {},
  methods: {
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
          this.savedJobsIds.push(job.id);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
          console.log(this.errors);
        });
      console.log(job);
    },
    indexSavedJobs: function () {
      axios.get("/saved_jobs").then((response) => {
        console.log("saved_jobs", response);
        this.savedJobsIds = response.data.map((savedJob) => {
          return savedJob.job.id;
        });
        console.log(this.savedJobsIds);
      });
    },
    isLoggedIn: function () {
      return localStorage.getItem("jwt");
    },
    currentPage: function () {
      console.log(window.location.hostname);
    },
    isSaved: function (job) {
      return this.savedJobsIds.includes(job.id);
    },
    beforeMount() {
      this.indexJobs();
      this.currentPage();
      this.indexSavedJobs();
    },
  },
};
</script>

<style scoped>
h4 {
  padding: 1em;
}
</style>
