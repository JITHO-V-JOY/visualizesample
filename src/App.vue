<script>
  import Visualize from './components/Visualize.vue'

  export default {
    name: 'App',
    components: {
      Visualize
    }
  }
</script>

<template>
  <div id="app" class="resources">

      <div class="resource dashboard">
        <div class="title">Supermart Dashboard</div>
        <Visualize
            resourceType="report"
            resourcePath="/public/Samples/Reports/AllAccounts"
        />
      </div>

    

  </div>
</template>

<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    margin-top: 20px;
  }
  .resources {
    display: flex;
  }
  .title {
    color: black;
    margin-bottom: 20px;
  }
  .resource {
    height: 500px;
  }
  .resource.dashboard {
    width: calc(70% - 25px);
    margin-right: 50px;
  }
  .resource.adhoc {
    width: calc(30% - 25px);
  }
</style>
