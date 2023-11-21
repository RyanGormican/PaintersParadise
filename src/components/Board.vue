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
        <div class="canvas-wrapper">
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
      paletteColors: ['#ff0000', '#ffae00', '#ffff5c', '#00ff00', '#0000ff', '#3f0fff'],
      drawingMode: 'dot',
      previewShape: null,
    };
  },
  methods: {
    setDrawingMode(mode) {
      this.drawingMode = mode;
    },
    startDrawing(event) {
      this.drawing = true;
      this.draw(event);
    },
    stopDrawing() {
      this.drawing = false;
      this.previewShape = null;
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
    draw(event) {
      const canvas = this.$refs.canvas;
      const context = canvas.getContext('2d');

      const rect = canvas.getBoundingClientRect();
      const x = event.clientX - rect.left;
      const y = event.clientY - rect.top;

      // Clear previous highlight
      context.clearRect(0, 0, canvas.width, canvas.height);

      // Draw preview
      if (this.drawingMode !== 'dot' && this.drawing) {
        this.previewShape = { x, y };
        this.drawShapePreview(context, x, y);
      }

      // Draw on canvas
      if (this.drawing) {
        context.fillStyle = this.selectedColor;

        if (this.drawingMode === 'dot') {
          context.fillRect(x, y, 5, 5);
        } else if (this.drawingMode === 'square') {
          // Draw square logic
        } else if (this.drawingMode === 'circle') {
          // Draw circle logic
        } else if (this.drawingMode === 'triangle') {
          // Draw triangle logic
        }
      }
    },
    drawShapePreview(context, x, y) {
      context.globalAlpha = 0.3;
      context.fillStyle = 'blue'; // Change color as needed

      if (this.drawingMode === 'square') {
        const size = 50;
        context.fillRect(x - size / 2, y - size / 2, size, size);
      } else if (this.drawingMode === 'circle') {
        const radius = 25;
        context.beginPath();
        context.arc(x, y, radius, 0, 2 * Math.PI);
        context.fill();
      } else if (this.drawingMode === 'triangle') {
        const size = 50;
        context.beginPath();
        context.moveTo(x, y - size / 2);
        context.lineTo(x + size / 2, y + size / 2);
        context.lineTo(x - size / 2, y + size / 2);
        context.closePath();
        context.fill();
      }

      context.globalAlpha = 1.0;
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

.palette div {
  width: 4.5vw;
  height: 4.5vh;
  cursor: pointer;
  border: 1px solid #000;
}
</style>
