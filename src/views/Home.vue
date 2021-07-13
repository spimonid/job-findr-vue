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
            <!-- <th scope="col">Skill Score</th> -->
          </tr>
        </thead>
        <tbody v-if="!getCheckedTechs.length">
          <tr v-for="job in this.jobs" v-bind:key="job.id">
            <td>{{ job.company.name }}</td>
            <td>{{ job.role }}</td>
            <td>{{ job.remote }}</td>
            <td>{{ job.technologies }}</td>

            <!-- <td>{{ job.full_description.split(" ")}}</td> -->

            <td>
              <div v-if="isLoggedIn()">
                <button type="button" v-if="!isSaved(job)" v-on:click="saveJob(job)">save job</button>
                <span v-else>saved!</span>
              </div>
            </td>
            <!-- <td v-if="isLoggedIn()">{{ job.technologies.includes("Ruby") }}</td> -->
          </tr>
        </tbody>
        <tbody v-else>
          <tr v-for="job in filterJobs" v-bind:key="job.id">
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
// @ is an alias to /src

export default {
  name: "Home",
  data: function () {
    return {
      jobs: [],
      savedJobsIds: [],
      checkedNames: [],
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
    isSaved: function (job) {
      return this.savedJobsIds.includes(job.id);
    },
    beforeMount() {
      this.indexJobs();
      this.currentPage();
      this.indexSavedJobs();
    },
    scanTechs: function (fu) {
      this.techs[fu] = !this.techs[fu];
      console.log(this.techs);
    },
    filterJobs: function () {
      console.log("JOB 1 - ", this.jobs[0]);
      return [this.jobs[0]];
    },
  },
};
</script>
