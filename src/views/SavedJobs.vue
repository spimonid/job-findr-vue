<template>
  <div class="saved_jobs">
    <h4>Your Saved Jobs</h4>
    <div class="container mt-4">
      <table class="table table-bordered">
        <thead>
          <tr>
            <th scope="col">Company</th>
            <th scope="col">Role</th>
            <th scope="col">Technologies</th>
            <th scope="col">Generate Cover Letter?</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="job in jobs" v-bind:key="job.id">
            <td>{{ job.job.company.name }}</td>
            <td>{{ job.job.role }}</td>
            <td>{{ job.job.technologies }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data: function () {
    return {
      jobs: [],
    };
  },
  created: function () {
    this.indexSavedJobs();
  },
  methods: {
    indexSavedJobs: function () {
      console.log(this.jobs);
      axios.get("/saved_jobs").then((response) => {
        console.log("saved_jobs", response);
        this.jobs = response.data;
        console.log(this.jobs);
      });
    },
    beforeMount() {
      this.indexSavedJobs();
      // this.indexWWR();
    },
  },
};
</script>
