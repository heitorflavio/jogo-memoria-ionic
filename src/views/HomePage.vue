<template>
  <div class="info">
    <span> Pontos: {{ points }}</span>
    <span>Tempo: {{ time }}</span>
  </div>
  <div class="memory-game">
    <div class="grid">
      <div v-for="(card, index) in cards" :key="index" :class="{ 'flipped': card.flipped }" @click="flipCard(index)">
        <div class="card">
          <div class="card-front">
            <img :src="card.value" alt="">
          </div>
          <div class="card-back"></div>
        </div>
      </div>
    </div>
    <button @click="resetGame">Reiniciar Jogo</button>
  </div>
</template>

<script>
import naruto from "@/assets/img/naruto.png"
import sakura from '@/assets/img/sakura.png'
import hinata from '@/assets/img/hinata.png'
import tsunade from '@/assets/img/tsunade.png'
import gara from '@/assets/img/gara.png'
import akatsuki from '@/assets/img/akatsuki.png'
import sasuke from '@/assets/img/sasuke.png'
import sharingan from '@/assets/img/sharingan.png'
import kakashi from '@/assets/img/kakashi.png'
/**
 * Este código é responsável por implementar um jogo de memória com cartas de personagens do anime "Naruto".
 * O jogador precisa encontrar pares de cartas idênticas para ganhar pontos. O objetivo é encontrar todos os pares
 * em um período de tempo.
 */
export default {
  data() {
    return {
      cards: [],         // Armazena as cartas do jogo (pares de imagens).
      flippedCards: [],  // Armazena as cartas viradas pelo jogador.
      points: 0,         // Armazena a pontuação do jogador.
      time: 0,           // Armazena o tempo decorrido do jogo.
    };
  },
  created() {
    // Inicializa o jogo quando o componente é criado.
    this.initializeGame();
  },
  methods: {
    /**
     * Inicializa o jogo, embaralhando as cartas e configurando o tempo.
     */
    initializeGame() {
      const values = [
        naruto,
        sakura,
        hinata,
        tsunade,
        gara,
        akatsuki,
        sasuke,
        sharingan,
        kakashi
      ];
      // Duplica os valores para criar pares de cartas.
      const cards = values.concat(values);
      // Embaralha o array de cartas.
      this.cards = this.shuffleArray(cards).map((value) => ({ value, flipped: false }));
      // Inicia o cronômetro.
      this.startTimer();
    },
    /**
     * Embaralha um array usando o algoritmo de Fisher-Yates.
     * @param {Array} array - O array a ser embaralhado.
     * @returns {Array} - O array embaralhado.
     */
    shuffleArray(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
      return array;
    },
    /**
     * Vira uma carta quando o jogador a seleciona.
     * @param {number} index - O índice da carta no array.
     */
    /**
 * Vira uma carta quando o jogador a seleciona e verifica se há uma correspondência.
 *
 * @param {number} index - O índice da carta no array 'cards'.
 */
    flipCard(index) {
      // Verifica se a carta não está virada e se o jogador não virou mais de duas cartas.
      if (!this.cards[index].flipped && this.flippedCards.length < 2) {
        this.cards[index].flipped = true; // Vira a carta selecionada.
        this.flippedCards.push(index); // Adiciona o índice da carta ao array de cartas viradas.

        // Se duas cartas estiverem viradas, verifica se são iguais.
        if (this.flippedCards.length === 2) {
          const [card1, card2] = this.flippedCards; // Obtém os índices das duas cartas viradas.

          if (this.cards[card1].value === this.cards[card2].value) {
            // As cartas coincidem, o jogador ganha pontos e as cartas permanecem viradas.
            this.points = this.points + 1; // Aumenta a pontuação do jogador.
            this.flippedCards = []; // Limpa o array de cartas viradas.
          } else {
            // As cartas não coincidem, aguarde um momento e, em seguida, vire-as de volta.
            this.points = this.points - 1; // Reduz a pontuação do jogador.
            setTimeout(() => {
              this.cards[card1].flipped = false; // Vira a primeira carta de volta.
              this.cards[card2].flipped = false; // Vira a segunda carta de volta.
              this.flippedCards = []; // Limpa o array de cartas viradas.
            }, 1000); // Aguarda 1 segundo antes de virar as cartas de volta.
          }
        }
      }

      // Verifica se todas as cartas foram viradas (todos os pares de cartas foram encontrados).
      const allFlipped = this.cards.every((card) => card.flipped);
      if (allFlipped) {
        setTimeout(() => {
          this.resetGame(); // Reseta o jogo após encontrar todos os pares de cartas.
        }, 1000); // Aguarda 1 segundo antes de reiniciar o jogo.
      }
    },
    /**
     * Reseta o jogo, redefinindo as cartas, a pontuação e o tempo.
     */
    resetGame() {
      this.points = 0;
      this.time = 0;
      this.cards.forEach((card) => (card.flipped = false)); // Vira todas as cartas de volta.
      this.flippedCards = [];
      this.initializeGame(); // Inicializa um novo jogo.
    },
    /**
     * Inicia o cronômetro do jogo.
     */
    startTimer() {
      // Atualiza o tempo a cada 1200 milissegundos (1,2 segundos).
      setInterval(() => {
        this.time = this.time += 1;
      }, 1200);
    },
  },
};

</script>


<style scoped>
.info {
  display: flex;
  justify-content: space-between;
  margin: 5px;
}

.memory-game {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 10px;
}

.grid {
  display: grid;
  grid-template-columns: repeat(3, 100px);
  gap: 5px;
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
