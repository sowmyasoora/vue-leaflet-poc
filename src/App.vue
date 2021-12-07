<template>
  <div id="app">
    <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
    <PostalCodePlot />
  </div>
</template>

<script>
import PostalCodePlot from './components/PostalCodePlot.vue'
import Vue from "vue";
import VueResource from 'vue-resource'
import "bootstrap";
import "bootstrap/dist/css/bootstrap.min.css";
Vue.use(VueResource)
Vue.config.productionTip = false;

export default {
  name: 'App',
  components: {
    PostalCodePlot
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
