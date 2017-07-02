<template>
  <div class="container">
    <div id="app">
      <div class="row">
          <div class="col-xs-12">
              <h1>NASA Image Search</h1>
          </div>
      </div>
      <search></search>
    </div>
  </div>
</template>

<script>
  import Search from './components/Search';

  export default {
    name: 'app',
    components: {
      Search
    }
  }
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
  }
  
  h1 {
    color: #004040;
    margin-top: 10px;
  }
</style>