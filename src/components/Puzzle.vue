<template>
  <div class="puzzle">
    <h1>15 puzzle game</h1>
    <div class="puzzle__field">
      <div class="puzzle__overlay" v-if="!isRunning">
        {{ gameMessage }}
      </div>
      <transition-group tag="ul" class="tiles">
        <li
          class="tile"
          v-for="tile in tiles"
          :class="tile == 0 ? 'zero-tile' : ''"
          v-bind:key="tile"
          @click="moveTile(tile)"
        >
          {{ tile }}
        </li>
      </transition-group>
    </div>
    <div class="puzzle__info">
      <button class="puzzle__btn" @click="startGame">Play</button>
      <span class="puzzle__score">🚶{{ moves }}</span>
      <span class="puzzle__time">⌛{{ displayTime }}</span>
      <button class="puzzle__btn" @click="stopGame">Stop</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "Puzzle",
  data: function() {
    return {
      gameMessage: "Press play to start",
      isRunning: false,
      moves: 0,
      timer: null,
      time: 0,
      tiles: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 0]
    };
  },

  computed: {
    displayTime: function() {
      let mins = Math.floor(this.time / 60);
      let secs = Math.floor(this.time % 60);

      if (secs.toString().length == 1) {
        secs = "0" + secs;
      }
      if (mins.toString().length == 1) {
        mins = "0" + mins;
      }
      return `${mins}:${secs}`;
    }
  },

  methods: {
    startGame() {
      this.moves = 0;
      this.isRunning = true;
      this.shuffle(this.tiles);
      this.startTimer();
    },

    stopGame() {
      this.isRunning = false;
      this.stopTimer();
    },

    startTimer() {
      this.time = 0;
      clearInterval(this.timer);
      this.timer = setInterval(this.incrementTime, 1000);
    },

    stopTimer() {
      clearInterval(this.timer);
    },

    incrementTime() {
      this.time++;
    },

    isCompleted() {
      return !this.tiles.some(function(item, i) {
        return item > 0 && item - 1 !== i;
      });
    },

    moveTile(t) {
      let index = this.tiles.findIndex(tile => t == tile);

      if (this.tiles[index - 1] == 0) {
        this.$set(this.tiles, index, 0);
        this.$set(this.tiles, index - 1, t);
        this.moves++;
      } else if (this.tiles[index - 4] == 0) {
        this.$set(this.tiles, index, 0);
        this.$set(this.tiles, index - 4, t);
        this.moves++;
      } else if (this.tiles[index + 1] == 0) {
        this.$set(this.tiles, index, 0);
        this.$set(this.tiles, index + 1, t);
        this.moves++;
      } else if (this.tiles[index + 4] == 0) {
        this.$set(this.tiles, index, 0);
        this.$set(this.tiles, index + 4, t);
        this.moves++;
      }

      if (this.isCompleted()) {
        this.gameMessage = `You win! In takes ${this.moves} steps, and ${this.displayTime}.`;
        this.stopGame();
      } else {
        this.gameMessage = "Press play to start";
      }
    },

    isSolvable() {
      let nullRow = Math.floor(this.tiles.findIndex(tile => tile == 0) / 4) + 1;

      let counter = nullRow;
      for (let i = 0; i < this.tiles.length; i++) {
        for (let j = i; j < this.tiles.length; j++) {
          if (this.tiles[i] < this.tiles[j] && this.tiles[i] !== 0) {
            counter++;
          }
        }
      }

      return counter % 2;
    },

    shuffle(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }

      if (this.isSolvable() !== 0) {
        return array;
      }

      this.shuffle(array);
    }
  }
};
</script>

<style lang="scss" scoped>
h3 {
  margin: 2em 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

.zero-tile {
  z-index: -1;
  background: #42b983;
  color: #42b983;
}

.puzzle {
  max-width: 400px;
  margin: 0 auto;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  position: relative;
}

.puzzle__field {
  transition: all 0.1s ease;
  width: 400px;
  height: 400px;
  padding: 0.5em;
  border-radius: 0.5em;
  border-radius: 20px;
  background: #42b983;
  box-shadow: 14px 14px 27px #389d6f, -14px -14px 27px #4cd597;
}

.puzzle__overlay {
  font-size: 2rem;
  text-align: center;
  display: block;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 400px;
  height: 400px;
  position: absolute;
  background: rgba(#42b983, 1);
  border-radius: 20px;
  z-index: 99;
}

.tiles {
  margin: 0;
  display: flex;
  flex-wrap: wrap;
}

.tile {
  position: relative;
  cursor: pointer;
  box-sizing: border-box;
  font-size: 2rem;
  margin: 1px;
  text-align: center;
  line-height: 2em;
  padding: 0.5em;
  display: block;
  width: 98px;
  height: 98px;
  background: #42b983;
  color: #ffff;
  border: 2px solid rgba(255, 255, 255, 0.2);
  border-radius: 20px;
  opacity: 1;
  transition: all 0.2s ease-in;

  &:hover {
    opacity: 0.5;
  }

  &:before {
    content: "";
    display: block;
    position: absolute;
    top: 5px;
    left: 5px;
    width: 80px;
    height: 80px;
    border-radius: 50%;
    z-index: 1;
    background: linear-gradient(145deg, #fff, #47c68c);
    opacity: 0.2;
  }
}

.puzzle__info {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  margin-top: 2em;
  width: 100%;
  padding: 0em;
}

.puzzle__score,
.puzzle__time {
  font-size: 20px;
  text-transform: uppercase;
  display: block;
  width: 30%;
}

.puzzle__btn {
  display: block;
  width: 80px;
  height: 80px;
  border-radius: 55px;
  border: none;
  background: #42b983;
  box-shadow: 11px 11px 21px #389d6f, -11px -11px 21px #4cd597;
  color: #ffff;
  font-size: 1rem;

  &:hover,
  &:focus {
    opacity: 0.6;
  }
  &:active {
    box-shadow: inset 11px 11px 21px #389d6f, inset -11px -11px 21px #4cd597;
  }
}
</style>
