<template>
  <div>
    <div class="info">
      <label for="fname">Scalable</label>
      <br />
      <input id="fname" name="fname" v-model="fieldSize" />
      <br />
      <b class="notes">Notes: please fill the bank with number</b>
      <br />
      <b class="notes">example: if you input 3, make the box 3x3</b>
      <br />
      <h3>{{ activePlayer }}'s turn</h3>
      <h3>Turn #{{ numTurns }}</h3>
      <br />
      <button @click="resetGame()">Reset Game</button>
    </div>
    <div class="win" v-if="!running">
      <h1 v-if="winner">Player {{ winner }} won after {{ numTurnsForPlayer(winner) }} turns!</h1>
      <h1 v-else>Draw!</h1>
    </div>
    <div class="field" v-if="state">
      <template v-for="(row, i) in state">
        <Box
          :fieldSize="fieldSize"
          :key="boxId(i,j)"
          :playerInfo="getPlayer(i,j)"
          @click.native="boxClicked(i,j)"
          v-for="(col, j) in row"
        ></Box>
      </template>
    </div>
  </div>
</template>

<script>
import Box from "./Box";

export default {
  components: { Box },
  data() {
    return {
      fieldSize: 0,
      symbols: ["X", "O"],
      colors: ["#0000ff", "#ff0000"],
      activePlayer: null,
      state: [],
      numTurns: 0,
      running: true,
      winner: null
    };
  },
  watch: {
    fieldSize(oldVal, newVal) {
      console.log(oldVal, newVal);
      for (let i = 0; i < this.fieldSize; i++) {
        this.state.length = this.fieldSize;
        this.state[i] = [];
        for (let j = 0; j < this.fieldSize; j++) {
          this.state[i].push("");
        }
      }

      this.activePlayer = this.symbols[0];
    }
  },
  methods: {
    resetGame() {
      this.state = [];
      this.fieldSize = 0;
      this.numTurns = 0;
    },
    boxClicked(i, j) {
      if (this.running && !this.state[i][j]) {
        this.state[i][j] = this.activePlayer;

        this.checkField();
        this.nextPlayer();
        this.$forceUpdate();

        this.running =
          this.running && ++this.numTurns < this.fieldSize * this.fieldSize;
      }
    },
    checkIsWin(arr) {
      arr = [...new Set(arr)];
      let win = arr.length === 1 && arr[0] !== "";
      if (win) {
        this.winner = arr[0];
        this.running = false;
      }
      return win;
    },
    checkRow(transposed = false) {
      let row = this.state;
      if (transposed) row = row.map((col, i) => this.state.map(row => row[i]));
      for (let i = 0; i < row.length; i++) {
        this.checkIsWin(row[i]);
      }
    },
    checkDiag() {
      this.checkIsWin(
        [...Array(this.state.length).keys()].map(
          i => this.state.flat()[i * this.fieldSize + i]
        )
      ); // left diagonal
      this.checkIsWin(
        [...Array(this.state.length).keys()].map(
          i => this.state.flat()[i * this.fieldSize + this.fieldSize - i - 1]
        )
      ); // right diagonal
    },
    checkField() {
      for (let s of this.symbols) {
        if (this.checkRow(false) || this.checkRow(true) || this.checkDiag())
          break;
      }
    },
    nextPlayer() {
      if (this.running) {
        let oldIndex = this.symbols.indexOf(this.activePlayer);
        this.activePlayer = this.symbols[
          oldIndex + 1 < this.symbols.length ? oldIndex + 1 : 0
        ];
      }
    },
    getPlayer(i, j) {
      let player = this.state[i][j];
      return {
        player,
        color: this.colors[this.symbols.indexOf(player)]
      };
    },
    boxId(i, j) {
      return `box-${i}-${j}`;
    },
    numTurnsForPlayer(s) {
      return this.state.flat().filter(x => x === s).length;
    }
  }
};
</script>

<style scoped>
.win {
  text-align: center;
}
.notes {
  font-size: 9px;
}
.field {
  width: 800px;
  height: 800px;
  margin: 0 auto;
}

.info {
  font-family: Roboto, sans-serif;
  font-size: 2em;
  position: absolute;
  top: 3em;
  left: 20px;
}
</style>
