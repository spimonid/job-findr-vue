<template>
  <div class="home container">
    <div class="row">
      <table class="col s10 highlight">
        <thead>
          <tr>
            <th scope="col"></th>
            <th scope="col">Company</th>
            <th scope="col">Role</th>
            <th scope="col">Remote</th>
            <th scope="col">Technologies</th>
            <th scope="col">Save Job?</th>
          </tr>
        </thead>
        <tbody v-if="!getCheckedTechs.length">
          <tr v-for="job in this.jobs" v-bind:key="job.id">
            <td><img :src="job.logo" /></td>
            <td>{{ job.company.name }}</td>
            <td>
              <a :href="job.link">{{ job.role }}</a>
            </td>
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
            <td>
              <a :href="job.link">{{ job.role }}</a>
            </td>
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
      </table>
      <div v-if="isLoggedIn()" class="filters col s2 m2">
        <h6>Filter by techs:</h6>
        <p v-for="tech in Object.keys(techs)" v-bind:key="tech">
          <label>
            <input type="checkbox" v-on:click="scanTechs(tech)" />
            <span>{{ tech }}</span>
          </label>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { VTooltip } from "v-tooltip";

export default {
  name: "Home",
  directives: { tooltip: VTooltip },
  data: function () {
    return {
      jobs: [],
      savedJobsIds: [],
      techs: {},
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
        this.setTechs(response.data);
      });
    },
    setTechs: function (jobs) {
      const techsArraySet = new Set(jobs.map((element) => element.technologies.split(" | ")).flat());
      const techsArray = Array.from(techsArraySet);
      this.techs = techsArray.reduce(function (techsDict, tech) {
        if (tech) {
          techsDict[tech] = false;
        }
        return techsDict;
      }, {});
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
    displayFullJobDescription: function (job) {
      return job.full_description;
    },
  },
};
</script>
<style scoped>
.filters {
  padding-left: 80px;
  padding-top: 20px;
}
</style>
