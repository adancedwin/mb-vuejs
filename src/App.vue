<template>
  <div id="app">
    <contentTable></contentTable>
  </div>
</template>

<script>
  import ContentTable from "./components/ContentTable";

  export default {
    name: 'App',
    components: {
      ContentTable
    },
    data() {
      return {}
    }
  }
</script>

<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
</style>
