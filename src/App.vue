<template>
  <div id="app">
    <nav>
      <div class="nav-wrapper #0d47a1 blue darken-4">
        <a href="#" class="brand-logo">JobFindr</a>
        <ul id="nav-mobile" class="right hide-on-med-and-down">
          <li class="nav-item" v-if="!isLoggedIn()">
            <router-link class="nav-link" to="/login">Login</router-link>
          </li>
          <li class="nav-item" v-if="isLoggedIn()">
            <router-link class="nav-link" to="/saved_jobs">Saved Jobs</router-link>
          </li>
          <li class="nav-item" v-if="isLoggedIn()">
            <router-link class="nav-link" to="/">Back To Job Feed</router-link>
          </li>
          <li class="nav-item" v-if="isLoggedIn()">
            <router-link class="nav-link" to="/logout">Logout</router-link>
          </li>
          <li class="nav-item" v-if="!isLoggedIn()">
            <router-link class="nav-link" to="/signup">Signup</router-link>
          </li>
          <!-- <a class="dropdown-trigger btn" href="/" data-target="dropdown1">Drop Me!</a>
          <ul id="dropdown1" class="dropdown-content">
            <li><a href="#!">one</a></li>
            <li><a href="#!">two</a></li>
            <li class="divider" tabindex="-1"></li>
            <li><a href="/">three</a></li>
            <li>
              <a href="#!">
                <i class="material-icons">view_module</i>
                four
              </a>
            </li>
            <li>
              <a href="#!">
                <i class="material-icons">cloud</i>
                five -->
          <!-- </a> -->
        </ul>
      </div>
    </nav>
    <router-view />
  </div>
</template>
<script>
export default {
  data: function () {
    return {
      // flashMessage: null,
    };
  },
  methods: {
    isLoggedIn: function () {
      return localStorage.getItem("jwt");
    },
  },
  components: {},
};
</script>
<style>
brand-logo {
  padding: 200px;
}
</style>
