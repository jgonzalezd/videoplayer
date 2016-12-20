<template>
  <div id="app">
    <!-- <img src="./assets/logo.png"> -->
    <VideoPlayer></VideoPlayer>
  </div>
</template>

<script>

import VideoPlayer from './components/VideoPlayer'

export default {
  name: 'app',
  components: { VideoPlayer }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
