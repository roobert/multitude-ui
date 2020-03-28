<template>
  <div id="app">
    <div class="header">
      <h1>Multitude</h1>
    </div>
    <Multitude
      v-bind:git_repos="git_repos"
      v-bind:docker_repos="docker_repos"
      v-bind:deployments="deployments"
    />
  </div>
</template>

<script>
import Multitude from './components/Multitude.vue';
import axios from 'axios';

// FIXME
// * use VueX?

export default {
  name: 'app',
  components: {
    Multitude
  },
  data() {
    return {
      interval: null,
      loading: true,
      git_repos: [],
      docker_repos: [],
      deployments: []
    }
  },
  created() {
    this.interval = setInterval(this.updateData, 2000)
  },
  beforeDestroy () {
    clearInterval(this.interval)
  },
  methods: {
    updateData() {
      axios.all([
        axios.get('http://localhost:8000/fetch/git_repo/'),
        axios.get('http://localhost:8000/fetch/docker_repo/'),
        axios.get('http://localhost:8000/fetch/deployment/')
      ])
      .then(axios.spread(
        (
          gitReposResponse,
          dockerReposResponse,
          deploymentsResponse
        ) => {
          this.git_repos = gitReposResponse.data,
          this.docker_repos = dockerReposResponse.data,
          this.deployments = deploymentsResponse.data
       }
      ))
      .catch((error) => {
        // eslint-disable-next-line
        console.error(error)
        this.errored = true, this.error = error
      })
      .finally(
        () => this.loading = false
      )
    }
  }
}
</script>

<style>
body {
  background: #fff;
  width: 100%;
  font-family: Arial, Helvetica, sans-serif;
}

.header h1 {
  color: #aaa;
  text-align: center;
  font-family: 'Lato', sans-serif;
  font-size: 30px;
  font-weight: 100;
  letter-spacing: 0.10em;
}

#app {
  width:800px;
  margin:60px auto;
}
</style>
