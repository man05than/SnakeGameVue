 <template>
  <div id="app">
    <h1>Snake Game</h1>
    <div class="column">
      cell Size (px):
      <input type="number" min="10" v-model.number="CellSize" />
    </div>
    <div class="column">
      Border Size (px):
      <input type="number" min="10" v-model.number="boardSize" />
    </div>
    <div class="column">
      Speed:
      <input type="number" min="5" v-model.number="speed" />
    </div>
    <SnakeCanvas
      :cellSize="CellSize"
      :boardSize="boardSize"
      :speed="speed"
      :isplaying="isplaying"
      :stop="stop"
      :addScores="addScores"
      :score="score"
    ></SnakeCanvas>
    <div>Scores: {{ score }}</div>
    <div>
      <button id="play-btn" v-on:click="isplaying ? stop() : start()">
        {{ isplaying ? "Stop" : "Play" }}
      </button>
    </div>
  </div>
</template>

<script>
import SnakeCanvas from "./components/SnakeCanvas/SnakeCanvas.vue";
export default {
  name: "App",
  components: {
    SnakeCanvas: SnakeCanvas,
  },
  data() {
    return {
      CellSize: 10,
      boardSize: 30,
      speed: 10,
      score: 0,
      isplaying: false,
    };
  },
  methods: {
    start() {
      this.isplaying = true;
    },
    stop() {
      this.isplaying = false;
      this.score = 0;
    },
    addScores(scores) {
      this.score += (scores / 2) * 5;
    },
  },
};
</script>

<style >
body {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  width: 100%;
}

.column {
  display: inline-block;
  width: 30%;
  background-color: whitesmoke;
  border-radius: 4px;
  padding: 5px;
  margin: 5px;
}

.column input {
  width: 30px;
  border-radius: 4px;
  border: 1px solid rgba(24, 23, 23, 0.308);
  line-height: 20px;
}

#play-btn {
  padding: 10px 20px;
  border: 1px solid gray;
  border-radius: 4px;
  font-size: 20px;
  margin-top: 10px;
  cursor: pointer;
}
</style>
