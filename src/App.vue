<template>
  <div id="app">
    <h1>Welcome to Tic Tac Toe Game</h1>
    <Game id="game" />
  </div>
</template>

<script>
import Game from "./components/Game.vue";

export default {
  name: "app",
  components: {
    Game
  }
};
</script>
<style scoped>
Game {
  margin-top: 30px;
}

h1 {
  text-align: center;
}
</style>