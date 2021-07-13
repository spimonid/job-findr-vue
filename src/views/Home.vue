<template>
  <div class="home">
    <div class="container mt-4">
      <table class="highlight">
        <thead>
          <div class="container">
            <h6>Filter by techs:</h6>
            <label>
              <input type="checkbox" v-on:click="scanTechs('Ruby')" />
              <span>Ruby</span>
            </label>
            <label>
              <input type="checkbox" v-on:click="scanTechs('Javascript')" />
              <span>Javascript</span>
            </label>
            <label>
              <input type="checkbox" v-on:click="scanTechs('Python')" />
              <span>Python</span>
            </label>
            <label>
              <input type="checkbox" v-on:click="scanTechs('Git')" />
              <span>Git</span>
            </label>
          </div>

          <tr>
            <th scope="col">Company</th>
            <th scope="col">Role</th>
            <th scope="col">Remote</th>
            <th scope="col">Technologies</th>
            <th scope="col">Save Job?</th>
          </tr>
        </thead>
        <tbody v-if="!getCheckedTechs.length">
          <tr v-for="job in this.jobs" v-bind:key="job.id">
            <td>{{ job.company.name }}</td>
            <td>{{ job.role }}</td>
            <td>{{ job.remote }}</td>
            <td>{{ job.technologies }}</td>

            <td>
              <div v-if="isLoggedIn()">
                <button type="button" v-if="!isSaved(job)" v-on:click="saveJob(job)">save job</button>
                <span v-else>saved!</span>
              </div>
            </td>
          </tr>
        </tbody>
        <tbody v-else>
          <tr v-for="job in this.filterJobs()" v-bind:key="job.id">
            <td>{{ job.company.name }}</td>
            <td>{{ job.role }}</td>
            <td>{{ job.remote }}</td>
            <td>{{ job.technologies }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Home",
  data: function () {
    return {
      jobs: [],
      savedJobsIds: [],
      techs: { Ruby: false, Python: false, React: false, Git: false },
    };
  },
  created: function () {
    this.indexJobs();
    this.indexSavedJobs();
  },
  components: {},
  computed: {
    getCheckedTechs() {
      var checkedTechs = [];
      for (const [tech, checked] of Object.entries(this.techs)) {
        if (checked === true) {
          checkedTechs.push(tech);
        }
      }
      console.log("checked Techs: ", checkedTechs);

      return checkedTechs;
    },
  },
  methods: {
    indexJobs: function () {
      axios.get("/jobs").then((response) => {
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
    },
    indexSavedJobs: function () {
      axios.get("/saved_jobs").then((response) => {
        this.savedJobsIds = response.data.map((savedJob) => {
          return savedJob.job.id;
        });
      });
    },
    isLoggedIn: function () {
      return localStorage.getItem("jwt");
    },
    isSaved: function (job) {
      return this.savedJobsIds.includes(job.id);
    },
    beforeMount() {
      this.currentPage();
    },
    scanTechs: function (fu) {
      this.techs[fu] = !this.techs[fu];
    },
    filterJobs: function () {
      const isInJobTechs = (job, currentValue) => job.technologies.includes(currentValue);

      const filteredJobs = this.jobs.filter((job) => {
        return this.getCheckedTechs.every((tech) => {
          return isInJobTechs(job, tech);
        });
      });
      return filteredJobs;
    },
  },
};
</script>
