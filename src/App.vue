<template>
  <div class="game">
    <div class="start-button">
      <button @click="startGame" :disabled="gameStarted">Start</button>
    </div>
    <div class="count">Count: {{ count }}</div>
    <div class="level-button">
      <label>
        <input type="radio" v-model="selectedSpeed" value="easy" @change="setSpeed('easy')"> Easy
      </label>
      <label>
        <input type="radio" v-model="selectedSpeed" value="normal" @change="setSpeed('normal')"> Normal
      </label>
      <label>
        <input type="radio" v-model="selectedSpeed" value="hard" @change="setSpeed('hard')"> Hard
      </label>
    </div>
    <div class="game-buttons">
      <button
        v-for="(button, index) in colors"
        :key="index"
        class="button"
        :style="{ 
          backgroundColor: button, 
          filter: `brightness(${(index === highlightedButtons[0] || index === highlightedButtons[1] || userClickedButton === index) ? 1.5 : 1})`
        }"
        @click="() => checkButton(index)"
        @mousedown="() => handleMouseDown(index)"
      @mouseup="handleMouseUp"
      ></button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      colors: ["darkred", "gold", "darkblue", "darkgreen"],
      sequence: [],
      userSequence: [],
      count: 0,
      gameStarted: false,
      highlightedButtons: [],
      sequenceIndex: 0,
      userClickedButton: null,
      speedOptions: {
        easy: 1500,
        normal: 1000,
        hard: 400
      },
      selectedSpeed: 'normal',
    };
  },
  methods: {
    startGame() {
      this.gameStarted = true;
      this.count = 0;
      this.sequence = [];
      this.highlightedButtons = [];
      this.sequenceIndex = 0;
      this.userClickedButton = null;
      this.playSequence();
    },
    playSequence() {
      this.sequence.push(this.getRandomButton());
      this.showSequence();
    },
    playSound(sound) {
  const audio = new Audio(require(`@/assets/sound/${sound}.mp3`));
  audio.play();
},
    showSequence() {
      if (this.sequenceIndex < this.sequence.length) {
        const color = this.sequence[this.sequenceIndex].color;
        this.highlightButton(color);
        this.playSound(color);

        setTimeout(() => {
          this.highlightedButtons = [];
          this.sequenceIndex++;
          this.showSequence();
        }, this.speedOptions[this.selectedSpeed]);
      } else {
        this.userSequence = [];
        this.sequenceIndex = 0;
      }
    },
    highlightButton(color) {
      const index = this.colors.indexOf(color);
      this.highlightedButtons = [index];
      setTimeout(() => {
        this.highlightedButtons = [];
      }, 500);
    },
    getRandomButton() {
      const randomIndex = Math.floor(Math.random() * this.colors.length);
      return { color: this.colors[randomIndex] };
    },

    handleMouseDown(index) {
      this.userClickedButton = index;
    },

    handleMouseUp() {
      setTimeout(() => {
        this.userClickedButton = null;
      }, 50);
    },

    playSoundOnClick(color) {
      const audio = new Audio(require(`@/assets/sound/${color}.mp3`));
      audio.play();
    },

    checkButton(index) {
      if (!this.gameStarted) return;
      this.userClickedButton = index;
      const clickedColor = this.colors[index];
      this.userSequence.push(clickedColor);

      this.playSoundOnClick(clickedColor); // воспроизвести звук при нажатии на кнопку

      if (clickedColor !== this.sequence[this.userSequence.length - 1].color) {
        this.endGame();
      } else if (this.userSequence.length === this.sequence.length) {
        this.count++;
        this.userSequence = [];
        this.sequenceIndex = 0;

        setTimeout(() => {
  if (this.userSequence.length === this.sequence.length) {
    setTimeout(() => {
      this.playSequence();
    }, 500);
  } else {
    this.playSequence();
  }
}, 1000);
      }
      setTimeout(() => {
        this.userClickedButton = null;
      }, 1000);
    },
    setSpeed(speed) {
      this.selectedSpeed = speed;
    },
    endGame() {
      this.gameStarted = false;
      alert("Game Over! Your score: " + this.count);
    },
  },
};
</script>

<style>
.game {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.game-buttons {
  display: flex;
  flex-wrap: wrap;
  margin-top: 20px;
  width: 200px;
  border: solid gray 3px;
  border-radius: 50%;
 overflow: hidden;
}

.button {
  width: 100px;
  height: 100px;
  transition: filter 0.1s ease; /* Анимация для изменения яркости */
  border: none;
}
</style>
