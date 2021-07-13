<template>
  <div id="app">
    <ul id="dropdown1" class="dropdown-content">
      <li><a href="#!">one</a></li>
      <li><a href="#!">two</a></li>
      <li class="divider"></li>
      <li><a href="#!">three</a></li>
    </ul>
    <nav>
      <div class="nav-wrapper #0d47a1 blue darken-4">
        <a href="#" class="brand-logo">JobFindr</a>
        <ul id="nav-mobile" class="right hide-on-med-and-down">
          <li class="nav-item" v-if="!isLoggedIn()">
            <router-link class="nav-link" to="/login">Login</router-link>
          </li>
          <li class="nav-item" v-if="isLoggedIn() && !onSaved()">
            <router-link class="nav-link" to="/saved_jobs">Saved Jobs</router-link>
          </li>
          <li class="nav-item" v-if="isLoggedIn() && onSaved()">
            <router-link class="nav-link" to="/">Back To Job Feed</router-link>
          </li>

          <li class="nav-item" v-if="isLoggedIn()">
            <router-link class="nav-link" to="/logout">Logout</router-link>
          </li>
          <li class="nav-item" v-if="!isLoggedIn()">
            <router-link class="nav-link" to="/signup">Signup</router-link>
          </li>
        </ul>
      </div>
    </nav>
    <router-view />
  </div>
</template>
<script>
// import $ from "jquery";
export default {
  // mounted: function () {
  //   $(document).ready(function () {
  //     console.log("HHHHHH", $(".dropdown-trigger").dropdown());
  //   });
  // },
  data: function () {
    return {
      // flashMessage: null,
    };
  },
  created: function () {
    this.onSaved();
  },
  methods: {
    isLoggedIn: function () {
      return localStorage.getItem("jwt");
    },
    onSaved: function () {
      return window.location.href === "http://localhost:8080/saved_jobs";
    },
  },
  components: {},
};
</script>
<style>
#brand-logo {
  padding: 2000px;
}
</style>
