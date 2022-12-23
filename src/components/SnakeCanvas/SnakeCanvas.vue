<template>
  <canvas
    ref="board"
    id="snake-canvas"
    :width="boardSizepx"
    :height="boardSizepx"
  >
  </canvas>
</template>
<script>
import constants from "./constant";

export default {
  name: "SnakeCanvas",
  props: {
    cellSize: Number,
    boardSize: Number,
    speed: Number,
    isplaying: Boolean,
    stop: Function,
    addScores: Function,
    score: Number,
  },
  computed: {
    boardSizepx() {
      return this.boardSize * this.cellSize;
    },
  },
  mounted() {
    this.boardContext = this.$refs.board.getContext("2d");
    window.addEventListener("keydown", this.onKeyPress);
  },
  created() {
    this.resetsnake();
  },
  beforeUnmount() {
    window.removeEventListener("keydown", this.onKeyPress);
  },
  watch: {
    isplaying(value) {
      this.clear();
      if (value) {
        this.resetsnake();
        this.move();
      }
    },
  },
  methods: {
    resetsnake() {
      this.snake = [
        {
          x: this.getmiddlecell(),
          y: this.getmiddlecell(),
        },
      ];
      const randomDirectionIndex = Math.floor(Math.random() * 4);
      this.direction = constants[randomDirectionIndex];
      this.targetCell = null;
    },
    getmiddlecell() {
      return Math.round(this.boardSize / 2);
    },
    move() {
      if (!this.isplaying) {
        return;
      }
      this.clear();
      this.setTargetCell();

      const newHeadCell = {
        x: this.snake[0].x + this.direction.move.x,
        y: this.snake[0].y + this.direction.move.y,
      };

      if (
        this.isCellOutofBoard(newHeadCell) ||
        this.amountCellInSnake(this.snake[0]) > 1
      ) {
        this.stop();
        alert(`Game Over! Yov've scored ${this.score} points.`);
      }

      if (this.isTargetNewHead()) {
        this.snake.unshift(this.targetCell);
        this.targetCell = null;
        this.addScores(this.speed);
      } else {
        this.snake.pop();
        this.snake.unshift(newHeadCell);
      }

      this.boardContext.beginPath();
      this.snake.forEach(this.drawCell);
      this.boardContext.closePath();

      setTimeout(this.move, this.getMoveDelay());
    },
    clear() {
      this.boardContext.clearRect(0, 0, this.boardSizepx, this.boardSizepx);
    },
    drawCell({ x, y }) {
      this.boardContext.rect(
        x * this.cellSize,
        y * this.cellSize,
        this.cellSize,
        this.cellSize
      );
      this.boardContext.fillStyle = "black";
      this.boardContext.fill();
    },
    getMoveDelay() {
      return (2 / Number(this.speed)) * 1000;
    },
    isCellOutofBoard({ x, y }) {
      return x < 0 || y < 0 || x >= this.boardSize || y >= this.boardSize;
    },
    onKeyPress(event) {
      const newDirection = constants.find((c) => c.keyCode === event.keyCode);

      if (!newDirection) {
        return;
      }

      if (Math.abs(newDirection.keyCode - this.direction.keyCode) !== 2) {
        this.direction = newDirection;
      }
    },
    setTargetCell() {
      if (!this.targetCell) {
        let targetCell = this.getRandomCell();
        while (this.amountCellInSnake(targetCell) > 0) {
          targetCell = this.getRandomCell;
        }
        this.targetCell = targetCell;
      }
      this.boardContext.beginPath();
      this.boardContext.rect(
        this.targetCell.x * this.cellSize,
        this.targetCell.y * this.cellSize,
        this.cellSize,
        this.cellSize
      );
      this.boardContext.fillStyle = "red";
      this.boardContext.fill();
      this.boardContext.closePath();
    },
    getRandomCell() {
      return {
        x: Math.floor(Math.random() * this.boardSize),
        y: Math.floor(Math.random() * this.boardSize),
      };
    },
    amountCellInSnake(cell) {
      return this.snake.filter(({ x, y }) => x === cell.x && y === cell.y)
        .length;
    },
    isTargetNewHead() {
      return (
        this.snake[0].x + this.direction.move.x === this.targetCell.x &&
        this.snake[0].y + this.direction.move.y === this.targetCell.y
      );
    },
  },
};
</script>
<style scoped>
#snake-canvas {
  border: 1px solid grey;
  margin: 10px 0;
  background-color: cyan;
}
</style>