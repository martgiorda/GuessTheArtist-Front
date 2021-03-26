<template>
  <html>
  <head>
    <title>Guess The Artist</title>
    <link rel="stylesheet" href="bootstrap.css">
  </head>
  <body>
    <div id= "app">
      <GamePage>
      </GamePage>
    </div>
  </body>
</html>
</template>

<script>
import GamePage from './components/GamePage.vue'

export default {
  name: 'App',
  components: {
    GamePage
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
