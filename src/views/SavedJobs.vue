<template>
  <div class="saved_jobs">
    <div class="container mt-4">
      <table class="table table-bordered">
        <thead>
          <tr>
            <th scope="col"></th>
            <th scope="col">Company</th>
            <th scope="col">Role</th>
            <th scope="col">Technologies</th>
            <th scope="col">Generate Cover Letter?</th>

            <th scope="col">Unsave?</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="job in jobs" v-bind:key="job.id">
            <td><img :src="job.job.logo" /></td>
            <td>{{ job.job.company.name }}</td>
            <td>{{ job.job.role }}</td>
            <td>{{ job.job.technologies }}</td>
            <!-- Modal Trigger -->
            <td>
              <a class="waves-effect waves-light btn modal-trigger" href="#modal1" v-on:click="currentJob = job">
                Generate
              </a>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <!-- Modal Structure -->
    <div id="modal1" class="modal modal-fixed-footer">
      <div class="modal-content">
        <h4>Modal Header</h4>
        <p>{{ currentJob.job.company.name }}</p>
      </div>
      <div class="modal-footer">
        <a href="#!" class="modal-close waves-effect waves-green btn-flat">Agree</a>
      </div>
    </div>
  </div>
</template>

<script>
/* global $ */
import axios from "axios";

export default {
  data: function () {
    return {
      jobs: [],
      currentJob: { job: { company: {} } },
    };
  },
  updated: function () {
    $(".modal").modal();
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
    },
  },
};
</script>
