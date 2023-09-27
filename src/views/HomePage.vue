<template>
  <div class="memory-game">
    <div class="grid">
      <div v-for="(card, index) in cards" :key="index" :class="{ 'flipped': card.flipped }" @click="flipCard(index)">
        <div class="card">
          <div class="card-front">{{ card.value }}</div>
          <div class="card-back"></div>
        </div>
      </div>
    </div>
    <p> Pontos: {{ points }}</p>
    <p>Tempo: {{ time }}</p>
    <button @click="resetGame">Reiniciar Jogo</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cards: [],
      flippedCards: [],
      points: 0,
      time: 0,
    };
  },
  created() {
    this.initializeGame();
  },
  methods: {
    initializeGame() {
      // Crie um conjunto de cartas duplicadas e embaralhe-as
      const values = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H'];
      const cards = values.concat(values);
      this.cards = this.shuffleArray(cards).map((value) => ({ value, flipped: false }));
      this.startTimer();
    },
    shuffleArray(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
      return array;
    },
    flipCard(index) {
      if (!this.cards[index].flipped && this.flippedCards.length < 2) {
        this.cards[index].flipped = true;
        this.flippedCards.push(index);

        if (this.flippedCards.length === 2) {
          const [card1, card2] = this.flippedCards;
          if (this.cards[card1].value === this.cards[card2].value) {
            // As cartas coincidem, mantenha-as viradas
            this.points = this.points + 1;
            this.flippedCards = [];
          } else {
            // As cartas não coincidem, aguarde um momento e, em seguida, vire-as de volta
            this.points = this.points - 1;
            setTimeout(() => {
              this.cards[card1].flipped = false;
              this.cards[card2].flipped = false;
              this.flippedCards = [];
            }, 1000);
          }
        }
      }

      // Verifique se todos os cards estão virados
      const allFlipped = this.cards.every((card) => card.flipped);
      if (allFlipped) {
        setTimeout(() => {
          this.resetGame();
        }, 1000);
      }
    },
    resetGame() {
      this.points = 0;
      this.time = 0;
      this.cards.forEach((card) => (card.flipped = false));
      this.flippedCards = [];
      this.initializeGame();
    },
    startTimer() {
      setInterval(() => {
        this.time = this.time += 1;
      }, 1000);
    },
  },
};
</script>


<style scoped>
.memory-game {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 20px;
}

.grid {
  display: grid;
  grid-template-columns: repeat(4, 100px);
  gap: 10px;
}

.grid>div {
  width: 100px;
  height: 100px;
  border: 1px solid #ccc;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 24px;
  cursor: pointer;
  perspective: 1000px;
}

.grid>div.flipped .card {
  transform: rotateY(0deg);
}

.card {
  transform: rotateY(180deg);
}

.card {
  width: 100%;
  height: 100%;
  transform-style: preserve-3d;
  transition: transform 0.3s ease;
}

.card-front,
.card-back {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
  backface-visibility: hidden;
}

.card-back {
  transform: rotateY(180deg);
  background-color: #ccc;
}

button {
  margin-top: 20px;
  padding: 10px 20px;
  font-size: 18px;
}
</style>
