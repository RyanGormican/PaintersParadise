<template>
  <div>
    <div class="button-container">
      <div class="button-container-left">
        <button class="button" @click="setDrawingMode('dot')">
          <Icon icon="mdi:dot" width="60" />
        </button>
        <button class="button" @click="setDrawingMode('square')">
          <Icon icon="material-symbols:square" width="60" />
        </button>
        <button class="button" @click="setDrawingMode('circle')">
          <Icon icon="material-symbols:circle" width="60" />
        </button>
        <button class="button" @click="setDrawingMode('triangle')">
          <Icon icon="mdi:triangle" width="60" />
        </button>
      </div>
      <div class="canvas-container">
        <div class="canvas-wrapper"  @mousemove="drawPreview">
          <canvas ref="canvas" @mousedown="startDrawing" @mousemove="draw" @mouseup="stopDrawing" @mouseleave="stopDrawing"></canvas>
        </div>
      </div>
      <div class="button-container-right">
        <button class="button" @click="clearCanvas">
          <Icon icon="ph:trash" width="60" />
        </button>
        <button class="button" @click="downloadCanvas">
          <Icon icon="material-symbols:download" width="60" />
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
  paletteColors: ['#ff0000', '#ffae00', '#ffff5c', '#00ff00', '#188533','#1df2f2','#0000ff', '#8b8ee0', '#3f0fff','#ee3ff2','ffffff','#000000',],
  drawingMode: 'dot',
  tempCanvas: null,
  tempContext: null,
  };
  },
  methods: {
  startDrawing(event) {
  this.drawing = true;
  this.draw(event);
  },
  setDrawingMode(mode) {
  this.drawingMode = mode;
  },
  stopDrawing() {
  this.drawing = false;
  },
  clearCanvas() {
  const canvas = this.$refs.canvas;
  const context = canvas.getContext('2d');

  context.fillStyle = 'white';
  context.fillRect(0, 0, canvas.width, canvas.height);
  },
  selectColor(color) {
  this.selectedColor = color;
  },
  downloadCanvas() {
  const canvas = this.$refs.canvas;
  const dataUrl = canvas.toDataURL('image/png');
  const link = document.createElement('a');
  link.href = dataUrl;
  link.download = 'drawing.png';
  link.click();
  },
  drawPreview(event) {
 
  },
  draw(event) {
  if (!this.drawing) return;

  const canvas = this.$refs.canvas;
  const context = canvas.getContext('2d');

  const rect = canvas.getBoundingClientRect();
  const x = event.clientX - rect.left;
  const y = event.clientY - rect.top;

  context.fillStyle = this.selectedColor;
  switch (this.drawingMode) {
  case 'dot':
  context.fillRect(x, y, 5, 5);
  break;
  case 'square':
  const squareSize = 30; 
  const squareX = x - squareSize / 2;
  const squareY = y - squareSize / 2;
  context.fillRect(squareX, squareY, squareSize, squareSize);
  break;
  case 'circle':
  const circleSize = 30; 
  context.beginPath();
  context.arc(x, y, circleSize / 2, 0, 2 * Math.PI);
  context.fill();
  context.closePath();
  break;
  case 'triangle':
  const triangleSize = 30; 
  context.beginPath();
  context.moveTo(x, y - triangleSize / 2);
  context.lineTo(x - (triangleSize / 2) * Math.sqrt(3) / 2, y + triangleSize / 2);
  context.lineTo(x + (triangleSize / 2) * Math.sqrt(3) / 2, y + triangleSize / 2);
  context.fill();
  context.closePath();
  break;
  default:
  break;
  }
  },
  },
  mounted() {
  const canvas = this.$refs.canvas;
  const context = canvas.getContext('2d');
  // Set canvas size
  canvas.width = 0.8 * window.innerWidth;
  canvas.height = 0.6 * window.innerHeight;

  // Center the canvas
  canvas.style.display = 'block';
  this.clearCanvas();
  // Set initial color
  context.fillStyle = this.selectedColor;
  },
  };
</script>

<style scoped="">
  .button-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  }

  .button-container-left,
  .button-container-right {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  background-color: transparent;
  }

  .button {
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
  .preview-canvas {
  position: absolute;
  top: 0;
  left: 0;
  pointer-events: none;
  }
  .palette div {
  width: 4vw;
  height: 4vh;
  cursor: pointer;
  border: 1px solid #000;
  }
</style>