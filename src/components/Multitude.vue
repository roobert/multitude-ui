<template>
  <div class="multitude">
    <table class="pure-table pure-table-bordered">
      <thead>
        <tr>
          <th class="application">Application</th>
          <th class="version">Version</th>
          <th class="pipeline">Pipeline</th>
          <th class="image">Image</th>
        </tr>
      </thead>
      <tbody>
        <tr v-bind:key="git_repo.id" v-for="git_repo in git_repos">
          <td>{{git_repo.owner}}/{{git_repo.repo}}</td>
          <td>{{git_repo.properties.tag}}</td>
          <td>{{git_repo.properties.status}}</td>
          <td>{{ dockerReposFilter(git_repo.owner, git_repo.repo, git_repo.properties.tag)[0].properties.status }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
// should Deployment table be part of the event list?
//
//  Application | Version | Pipeline | Image | Deployment
// -------------+---------+----------+-------+--------------------------------------------------------------
//              |         |          |       |   region:environment:deployment:cluster:application:container
//              |         |          |       |   region:environment:deployment:cluster:application:container
//              |         |          |       |   region:environment:deployment:cluster:application:container
//
// separate deployment table design:
//
//  region:environment:deployment | cluster | application | container | version
// -------------------------------+---------+-------------+-----------+----------
//                                |         |             |           |
//                                |         |             |           |
//                                |         |             |           |
//                                |         |             |           |
//
//

export default {
  name: 'Multitude',
  props: {
    git_repos: {},
    docker_repos: {},
    deployments: {}
  },
  methods: {
    dockerReposFilter: function(owner, repo, tag) {
      return this.docker_repos
        .filter(function(docker_repo) {
          return docker_repo.owner == owner;
        })
        .filter(function(docker_repo)
        {
          return docker_repo.repo == repo;
        })
        .filter(function(docker_repo)
        {
          return docker_repo.properties.tag == tag;
        });
    }
  }
}
</script>

<style scoped>
table {}

th {
  text-align: left;
  padding: 8px;
}

th.application {
  width: 300px;
}

th.version {
  width: 80px;
}

th.pipeline {
  width: 180px;
}

th.image {
  width: 240px;
}

td {
  padding: 8px;
}
</style>
