<template>
  <div
    class="game-wrapper"
    style="max-width: 1200px; margin: 0 auto; padding: 20px"
  >
    <header style="text-align: center; margin-bottom: 30px">
      <h1 class="swift-title" style="font-size: 2.8rem; margin-bottom: 5px">
        🎵 Taylor's Memory Game 🌸
      </h1>
      <p style="color: #8c6275; font-style: italic">
        Empareja cada canción con su respectivo álbum o era
      </p>

      <div
        style="
          display: flex;
          justify-content: center;
          gap: 20px;
          margin-top: 15px;
          font-weight: 600;
        "
      >
        <div
          style="
            background: white;
            padding: 8px 18px;
            border-radius: 20px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.05);
          "
        >
          ✨ Puntos: <span style="color: #ff85a1">{{ score }}</span>
        </div>
        <div
          style="
            background: white;
            padding: 8px 18px;
            border-radius: 20px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.05);
          "
        >
          🫶 Intentos: {{ attempts }}
        </div>
        <div
          style="
            background: white;
            padding: 8px 18px;
            border-radius: 20px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.05);
            color: #d32f2f;
          "
        >
          ⏳ Tiempo: {{ timeLeft }}s
        </div>
        <div
          v-if="streak > 1"
          style="
            background: #fff0f5;
            padding: 8px 18px;
            border-radius: 20px;
            box-shadow: 0 4px 10px rgba(255, 105, 180, 0.2);
            color: #ff1493;
          "
        >
          🔥 Racha x{{ streak }}
        </div>
      </div>
    </header>

    <div
      v-if="!gameStarted"
      style="
        text-align: center;
        background: white;
        padding: 40px;
        border-radius: 24px;
        box-shadow: 0 10px 30px rgba(0, 0, 0, 0.05);
        max-width: 450px;
        margin: 0 auto;
      "
    >
      <h3
        style="
          margin-top: 0;
          font-family: 'Playfair Display', serif;
          font-size: 1.4rem;
        "
      >
        Configura tu tablero
      </h3>
      <p style="font-size: 0.85rem; color: #8c6275; margin-bottom: 20px">
        Indica cuántas parejas deseas buscar (Máx 30):
      </p>

      <input
        type="number"
        v-model.number="pairsCount"
        min="2"
        max="30"
        style="
          padding: 10px;
          width: 80px;
          border-radius: 10px;
          border: 2px solid #ffc8dd;
          text-align: center;
          font-size: 1.1rem;
          margin-bottom: 25px;
          outline: none;
        "
      />
      <br />
      <button
        @click="initGame"
        style="
          background: linear-gradient(135deg, #ff85a1 0%, #ffafcc 100%);
          color: white;
          border: none;
          padding: 12px 35px;
          border-radius: 25px;
          font-weight: bold;
          cursor: pointer;
          box-shadow: 0 4px 15px rgba(255, 133, 161, 0.3);
        "
      >
        Comenzar Juego ✨
      </button>
    </div>

    <div v-else>
      <div
        style="
          display: flex;
          justify-content: space-between;
          align-items: center;
          margin-bottom: 20px;
        "
      >
        <button
          @click="gameStarted = false"
          style="
            background: transparent;
            border: 2px solid #ffc8dd;
            color: #8c6275;
            padding: 6px 15px;
            border-radius: 12px;
            cursor: pointer;
            font-weight: 600;
          "
        >
          ↩ Volver al menú
        </button>
        <span
          v-if="victory"
          class="swift-title"
          style="font-size: 1.3rem; color: #4caf50"
        >
          ¡Completado! Eres una auténtica Swiftie 🥹🫶
        </span>
        <span
          v-if="gameOver && !victory"
          class="swift-title"
          style="font-size: 1.3rem; color: #d32f2f"
        >
          ¡Se acabó el tiempo! Inténtalo de nuevo 💔
        </span>
      </div>

      <div
        style="
          display: grid;
          grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
          gap: 15px;
        "
      >
        <div v-for="card in cards" :key="card.uniqueId" class="card-container">
          <div
            class="swift-card"
            :class="{ 'is-flipped': card.isFlipped || card.isMatched }"
            @click="flipCard(card)"
          >
            <div class="card-face card-back">
              <div class="ts-logo">TS</div>
              <div class="ts-sub-logo">Taylor Swift</div>
            </div>

            <div
              class="card-face card-front"
              :style="{
                backgroundColor: getAlbumStyle(card.album).color,
                color: getAlbumStyle(card.album).text,
              }"
            >
              <span
                style="
                  font-size: 0.6rem;
                  text-transform: uppercase;
                  letter-spacing: 1.2px;
                  opacity: 0.7;
                  margin-bottom: 5px;
                "
              >
                {{ card.album }}
              </span>
              <strong style="font-size: 0.9rem; line-height: 1.3">
                {{ card.type === 'song' ? '"' + card.content + '"' : 'Era Card' }}
              </strong>
              <span
                v-if="card.type === 'album'"
                style="
                  font-family: 'Playfair Display', serif;
                  font-style: italic;
                  font-size: 1rem;
                  margin-top: 2px;
                "
              >
                {{ card.content }}
              </span>
              <div v-if="getAlbumStyle(card.album).isTV" class="tv-badge">
                TV 🍂
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { songsList, albumsData } from "../data/songs";

export default {
  data() {
    return {
      songsList,
      albumsData,
      pairsCount: 8,
      gameStarted: false,
      cards: [],
      selectedCards: [],
      score: 0,
      attempts: 0,
      lockBoard: false,
      timer: null,
      timeLeft: 60,
      streak: 0,
      gameOver: false
    };
  },
  computed: {
    victory() {
      return (
        this.cards.length > 0 && this.cards.every((card) => card.isMatched)
      );
    },
  },
  unmounted() {
    clearInterval(this.timer);
  },
  methods: {
    getAlbumStyle(albumName) {
      return (
        this.albumsData[albumName] || {
          color: "#ffffff",
          text: "#000000",
          isTV: false,
        }
      );
    },
    initGame() {
      if (this.pairsCount < 2) this.pairsCount = 2;
      if (this.pairsCount > 30) this.pairsCount = 30;

      const shuffledSongs = [...this.songsList].sort(() => 0.5 - Math.random());
      const selectedSongs = shuffledSongs.slice(0, this.pairsCount);

      let gameCards = [];
      selectedSongs.forEach((song, index) => {
        gameCards.push({
          uniqueId: `song-${index}`,
          pairId: song.id,
          type: "song",
          content: song.name,
          album: song.album,
          isFlipped: false,
          isMatched: false,
        });
        gameCards.push({
          uniqueId: `album-${index}`,
          pairId: song.id,
          type: "album",
          content: song.album,
          album: song.album,
          isFlipped: false,
          isMatched: false,
        });
      });

      this.cards = gameCards.sort(() => 0.5 - Math.random());
      this.score = 0;
      this.attempts = 0;
      this.selectedCards = [];
      this.gameStarted = true;
      this.lockBoard = false;
      this.streak = 0;
      this.gameOver = false;

      // Configuración del Temporizador (Tarea 4)
      this.timeLeft = this.pairsCount * 9; 
      clearInterval(this.timer);
      this.timer = setInterval(() => {
        if (this.timeLeft > 0) {
          this.timeLeft--;
        } else {
          this.handleTimeout();
        }
      }, 1000);
      // Añadir al final de tu función initGame() existente:
this.timeLeft = this.pairsCount * 9; // 9 segundos asignados por pareja en tablero
this.gameOver = false;
this.streak = 0;
clearInterval(this.timer);
this.timer = setInterval(() => {
  if (this.timeLeft > 0) {
    this.timeLeft--;
  } else {
    this.handleTimeout();
  }
}, 1000);
    },
    handleTimeout() {
      clearInterval(this.timer);
      this.gameOver = true;
      this.lockBoard = true;
      this.cards.forEach((c) => (c.isFlipped = true)); 
    },
    flipCard(card) {
      if (this.lockBoard || card.isFlipped || card.isMatched || this.gameOver) return;

      card.isFlipped = true;
      this.selectedCards.push(card);

      if (this.selectedCards.length === 2) {
        this.checkMatch();
      }
    },
    checkMatch() {
      this.attempts++;
      const [card1, card2] = this.selectedCards;

      if (card1.pairId === card2.pairId && card1.type !== card2.type) {
        card1.isMatched = true;
        card2.isMatched = true;
        this.streak++;
        this.score += 10 * this.streak; // Multiplicador por racha (Tarea 4)
        this.selectedCards = [];

        if (this.victory) {
          clearInterval(this.timer);
        }
      } else {
        this.lockBoard = true;
        this.streak = 0; // Se rompe la racha (Tarea 4)
        this.score = Math.max(0, this.score - 2);

        setTimeout(() => {
          card1.isFlipped = false;
          card2.isFlipped = false;
          this.selectedCards = [];
          this.lockBoard = false;
        }, 1200);
      }
    },
  },
};
</script>