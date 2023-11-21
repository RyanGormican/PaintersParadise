<template>
  <div>
    <div class="canvas-container">
      <div class="canvas-wrapper">
        <canvas ref="canvas" @mousedown="startDrawing" @mousemove="draw" @mouseup="stopDrawing" @mouseleave="stopDrawing"></canvas>
      </div>
      <div class="button-container">
        <button class="clear-button" @click="clearCanvas">
          <Icon icon="ph:trash" width="60" />
        </button>
      </div>
    </div>
    <div class="palette">
      <div v-for="(color, index) in paletteColors" :key="index" @click="selectColor(color)" :style="{ backgroundColor: color }"></div>
    </div>
  </div>
</template>

<script>
  import { Icon } from '@iconify/vue';

  export default {
  components: {
  Icon,
  },
  data() {
  return {
  drawing: false,
  selectedColor: 'red',
  paletteColors: ['#ff0000', '#ffae00', '#ffff5c', '#00ff00', '#0000ff', '#3f0fff'],
  };
  },
  methods: {
  startDrawing(event) {
  this.drawing = true;
  this.draw(event);
  },
  stopDrawing() {
  this.drawing = false;
  },
  clearCanvas() {
  const canvas = this.$refs.canvas;
  const context = canvas.getContext('2d');

  context.clearRect(0, 0, canvas.width, canvas.height);
  },
  selectColor(color) {
  this.selectedColor = color;
  },
  draw(event) {
  if (!this.drawing) return;

  const canvas = this.$refs.canvas;
  const context = canvas.getContext('2d');

  const rect = canvas.getBoundingClientRect();
  const x = event.clientX - rect.left;
  const y = event.clientY - rect.top;

  context.fillStyle = this.selectedColor;
  context.fillRect(x, y, 5, 5);
  },
  },
  mounted() {
  const canvas = this.$refs.canvas;
  const context = canvas.getContext('2d');

  // Set canvas size
  canvas.width = 0.6 * window.innerWidth;
  canvas.height = 0.6 * window.innerHeight;

  // Center the canvas
  canvas.style.display = 'block';

  // Set initial color
  context.fillStyle = this.selectedColor;
  },
  };
</script>

<style scoped="">
  .canvas-container {
  display: flex;
  justify-content: center;
  }

  .canvas-wrapper {
  position: relative;
  }

  .button-container {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  background-color: transparent;
  }

  .clear-button {
  border: none;
  cursor: pointer;
  outline: none;
  background-color: transparent;
  }

  canvas {
  border: 3px solid #000;
  }

  .palette {
  display: flex;
  margin: auto;
  justify-content: center;
  }

  .palette div {
  width: 4vw;
  height: 4vh;
  cursor: pointer;
  border: 1px solid #000;
  }
</style>

