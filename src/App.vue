<template>
  <HomePage />
</template>

<script>
import HomePage from './components/HomePage.vue'

export default {
  name: 'App',
  components: {
    HomePage
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 20px;
  font-family: BR-Candor-Regular;
}
@font-face {
  font-family: "BR-Candor-Regular";
  src: local("BR-Candor-Regular"), url(./assets/fonts/BR-Candor-Regular.otf) format("opentype");
}
@font-face {
  font-family: "BR-Candor-SemiBold";
  src: local("BR-Candor-SemiBold"), url(./assets/fonts/BR-Candor-SemiBold.otf) format("opentype");
}
</style>
