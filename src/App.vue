<template>
  <div id="app">
    <message-board/>
  </div>
</template>

<script>
import MessageBoard from './components/message-board/index.vue'

export default {
  name: 'App',
  components: {
    MessageBoard
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
