<template>
  <div class="whackamole">
    <h1 class="logo">
      Whack-a-mole!
    </h1>
    <button
      class="start-game"
      :class='getButtonActive'
      :disabled='gameActive'
      @click='startGame'
    >
      Start Game
    </button>
    <div class="counters-container">
      <Counter label='Score' :count='score'></Counter>
      <Counter label='High Score' :count='highScore'></Counter>
      <Counter label='Timer' :count='timer'></Counter>
    </div>
    <div class="moles-container" v-bind:class='gameActiveObj'>
      <Mole v-for='(item, index) in moles' v-bind:key='index' v-bind:active='item' @hit='hitCount'></Mole>
    </div>
  </div>
</template>

<script>
import Counter from './components/Counter';
import Mole from './components/Mole';
export default {
  name: 'App',
  components: {
    Counter,
    Mole,
  },
  data: function() {
    return {
      score: 0,
      highScore: 0,
      timer: 20,
      moles: [false, false, false, false],
      gameActive: false,
    };
  },
  computed: {
    gameActiveObj: function() {
      return {
        'game-active': this.gameActive,
      };
    },
    getButtonActive: function() {
      return {
        buttonActive: this.gameActive,
      };
    }
  },
  methods: {
    resetStatus: function() {
      this.score = 0;
      this.timer = 20;
      this.moles = [false, false, false, false];
    },
    startGame: function() {
      this.resetStatus();
      this.gameActive = true;
      this.appearMole();
      this.startTimer();
    },
    endGame: function() {
      this.gameActive=false;
      this.stopTimer();
      this.stopMoles();
      this.updateHighScore();
    },
    startTimer: function() {
      this.timeId = setInterval(()=>{
        this.countTime();
      }, 1000);
    },
    appearMole: function() {
      this.moleInterval = setInterval(this.randomMole.bind(this), 400);
    },
    activateMole: function(moleId) {
      this.toggleMole(moleId, true);
      setTimeout(() => this.deactivateMole(moleId), 1500);
    },
    randomMole: function() {
      const randomMoleIndex = Math.floor(Math.random() * this.moles.length);
      if (!this.moles[randomMoleIndex]) {
        this.activateMole(randomMoleIndex);
      }
    },
    stopTimer: function() {
      clearInterval(this.timeId);
    },
    countTime: function() {
      this.timer--;
      if(this.timer === 0) {
        this.endGame();
      }
    },
    stopMoles: function() {
      clearInterval(this.moleInterval);
    },
    hitCount: function(moleId) {
      this.score = this.score + 1;
      this.deactivateMole(moleId);
    },
    deactivateMole: function(moleId) {
      this.toggleMole(moleId, false);
    },
    toggleMole: function(moleId, showAction) {
      const moles = this.moles.slice();
      moles[moleId] = showAction;
      this.moles = moles;
    },
    updateHighScore: function() {
      this.highScore = Math.max(this.highScore, this.score);
    },
  }
};
</script>

<style>
.whackamole {
  font-family: 'Bungee', sans-serif;
  max-width: 960px;
  margin: auto;
  text-align: center;
}

.start-game {
  font-family: 'Bungee', sans-serif;
  padding: 20px;
  border-radius: 3px;
  border: 0;
  background-color: #52b1d6;
  color: #fff;
  font-size: 1em;
  cursor: pointer;
}

.buttonActive {
  opacity: 0.5;
}

.counters-container {
  display: flex;
  justify-content: space-evenly;
}

.moles-container {
  margin-top: 20px;
  display: flex;
  justify-content: space-between;
  opacity: 0.5;
  transition: opacity 0.3s ease;
}

.moles-container.game-active {
  opacity: 1;
}
</style>
