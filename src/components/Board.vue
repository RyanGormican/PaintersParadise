<template>
  <div>
    <div class="button-container">
      <div class="button-container-left">
        <button class="button" @click="setDrawingMode('square')">
          <Icon icon="material-symbols:square" width="60" />
        </button>
        <button class="button" @click="setDrawingMode('circle')">
          <Icon icon="material-symbols:circle" width="60" />
        </button>
        <button class="button" @click="setDrawingMode('triangle')">
          <Icon icon="mdi:triangle" width="60" />
        </button>
        <button class="button" @click="setDrawingMode('rectangle')">
          <Icon icon="material-symbols:rectangle" width="60" />
        </button>
        <button class="button" @click="setDrawingMode('heart')">
          <Icon icon="mdi:heart" width="60" />
        </button>
        <button class="button" @click="setDrawingMode('diamond')">
          <Icon icon="ph:diamond-fill" width="60" />
        </button>
        <button class="button" @click="setDrawingMode('ellipse')">
          <Icon icon="mdi:ellipse" width="60" />
        </button>
      </div>
     <div class="canvas-container">
    <div class="canvas-wrapper" @mousemove="drawPreview($event, $refs.canvas)">
      <canvas ref="canvas" @mousedown="startDrawing" @mousemove="draw" @mouseup="stopDrawing" @mouseleave="stopDrawing"></canvas>
    </div>
    <div class="canvas-wrapper preview-canvas"  :style="{ opacity: 0.9 }"  @mousemove="drawPreview($event, $refs.canvas)">
      <canvas ref="previewCanvas"></canvas>
    </div>
      </div>
      <div class="button-container-right">
        <button class="button" @click="clearCanvas">
          <Icon icon="ph:trash" width="60" />
        </button>
        <button class="button" @click="downloadCanvas">
          <Icon icon="material-symbols:download" width="60" />
        </button>
        <button class="button" @click="incrementSize">
          <Icon icon="mdi:increment" width="60" />
        </button>
        <button class="button" @click="decrementSize">
          <Icon icon="mdi:decrement" width="60" />
        </button>
      </div>
    </div>
    <div class="palette">
      <div v-for="(color, index) in paletteColors" :key="index" @click="selectColor(color)" :style="{ backgroundColor: color }"></div>
    </div>
    <div class="palettecustom">
      <div
        v-for="(color, index) in customPalette"
        :key="index"
        :style="{ backgroundColor: color }"
        @click="selectColor(color)"
      >
        <button class="button">
          <Icon icon="solar:palette-bold" width="20" />
          <input type="color" v-model="customPalette[index]" @change="applyColor(index)" />
        </button>
      </div>
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
  paletteColors: ['#ff0000','#ff6f60','#ffa06a', '#ffae00','#ffd702','#ffff5c','#00ff00', '#32cd33', '#32cd32',  '#188533','#1df2f2','#6495ee',  '#0000ff', '#8b8ee0','#3f0fff','#ee3ff2','white', '#808081','#70553e','#000000',],
  drawingMode: 'square',
  tempCanvas: null,
  size: 5,
  tempContext: null,
  customPalette: Array.from({ length: 20 }, () => 'white'),
  selectedCustomPaletteIndex: null,
  previewContext: null,
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
  applyColor(index) {
  this.selectedColor = this.customPalette[index];
  },
  downloadCanvas() {
  const canvas = this.$refs.canvas;
  const dataUrl = canvas.toDataURL('image/png');
  const link = document.createElement('a');
  link.href = dataUrl;
  link.download = 'drawing.png';
  link.click();
  },
  drawPreview(event,realCanvas) {
  const previewCanvas = this.$refs.previewCanvas;
  const previewContext = previewCanvas.getContext('2d');

  previewContext.clearRect(0, 0, previewCanvas.width, previewCanvas.height);
  previewContext.fillStyle = this.selectedColor;
  previewCanvas.opacity = 0.6;
  previewCanvas.width = realCanvas.width;
  previewCanvas.height = realCanvas.height;
  const rect = previewCanvas.getBoundingClientRect();
  const x = event.clientX - rect.left;
  const y = event.clientY - rect.top;

  switch (this.drawingMode) {
  case 'square':
  const squareSize = this.size;
  const squareX = x - squareSize / 2;
  const squareY = y - squareSize / 2;
  previewContext.fillRect(squareX, squareY, squareSize, squareSize);
  break;
  case 'circle':
  const circleSize = this.size;
  previewContext.beginPath();
  previewContext.arc(x, y, circleSize / 2, 0, 2 * Math.PI);
  previewContext.fill();
  previewContext.closePath();
  break;
  case 'triangle':
  const triangleSize = this.size;
  previewContext.beginPath();
  previewContext.moveTo(x, y - triangleSize / 2);
  previewContext.lineTo(x - (triangleSize / 2) * Math.sqrt(3) / 2, y + triangleSize / 2);
  previewContext.lineTo(x + (triangleSize / 2) * Math.sqrt(3) / 2, y + triangleSize / 2);
  previewContext.fill();
  previewContext.closePath();
  break;
  case 'rectangle':
  const rectangleWidth = this.size*(4/3);
  const rectangleHeight = this.size;
  const rectangleX = x - rectangleWidth / 2;
  const rectangleY = y - rectangleHeight / 2;
  previewContext.fillRect(rectangleX, rectangleY, rectangleWidth, rectangleHeight);
  break;
  case 'heart':
  const heartWidth = this.size;
  const heartHeight = this.size;

  previewContext.beginPath();
  previewContext.moveTo(x, y - heartHeight / 2);
  previewContext.bezierCurveTo(x + heartWidth / 2.5, y - heartHeight, x + heartWidth / 4, y - heartHeight / 3, x, y);
  previewContext.bezierCurveTo(x - heartWidth / 4, y - heartHeight / 3, x - heartWidth / 2.5, y - heartHeight, x, y - heartHeight / 2);
  previewContext.bezierCurveTo(x, y - heartHeight / 2.2, x, y - heartHeight / 2.5, x, y - heartHeight / 2);
  previewContext.closePath();
  previewContext.fill();
  break;
  case 'diamond':
  const diamondSize = this.size;
  previewContext.beginPath();
  previewContext.moveTo(x, y - diamondSize / 2);
  previewContext.lineTo(x + diamondSize / 2, y);
  previewContext.lineTo(x, y + diamondSize / 2);
  previewContext.lineTo(x - diamondSize / 2, y);
  previewContext.closePath();
  previewContext.fill();
  break;
  case 'ellipse':
  const ellipseWidth = this.size * 2;
  const ellipseHeight = this.size;
  previewContext.beginPath();
  previewContext.ellipse(
  x,
  y,
  ellipseWidth / 2,
  ellipseHeight / 2,
  0,
  0,
  2 * Math.PI
  );
  previewContext.fill();
  previewContext.closePath();
  break;
  default:
  break;
  }
  },
  incrementSize() {
  this.size = this.size+ 1;
  },
  decrementSize() {
  this.size = Math.max(1, this.size - 1);
  },
  handleScroll(event) {
  if (event.deltaY > 0) {
  this.decrementSize();
  } else {
  this.incrementSize();
  }
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
  case 'square':
  const squareSize =  this.size;
  const squareX = x - squareSize / 2;
  const squareY = y - squareSize / 2;
  context.fillRect(squareX, squareY, squareSize, squareSize);
  break;
  case 'circle':
  const circleSize =  this.size;
  context.beginPath();
  context.arc(x, y, circleSize / 2, 0, 2 * Math.PI);
  context.fill();
  context.closePath();
  break;
  case 'triangle':
  const triangleSize =  this.size;
  context.beginPath();
  context.moveTo(x, y - triangleSize / 2);
  context.lineTo(x - (triangleSize / 2) * Math.sqrt(3) / 2, y + triangleSize / 2);
  context.lineTo(x + (triangleSize / 2) * Math.sqrt(3) / 2, y + triangleSize / 2);
  context.fill();
  context.closePath();
  break;
  case 'rectangle':
  const rectangleWidth =  this.size*(4/3);
  const rectangleHeight =  this.size;
  const rectangleX = x - rectangleWidth / 2;
  const rectangleY = y - rectangleHeight / 2;
  context.fillRect(rectangleX, rectangleY, rectangleWidth, rectangleHeight);
  break;
  case 'heart':
  const heartWidth = this.size;
  const heartHeight = this.size;

  context.beginPath();
  context.moveTo(x, y - heartHeight / 2);
  context.bezierCurveTo(x + heartWidth / 2.5, y - heartHeight, x + heartWidth / 4, y - heartHeight / 3, x, y);
  context.bezierCurveTo(x - heartWidth / 4, y - heartHeight / 3, x - heartWidth / 2.5, y - heartHeight, x, y - heartHeight / 2);
  context.bezierCurveTo(x, y - heartHeight / 2.2, x, y - heartHeight / 2.5, x, y - heartHeight / 2);
  context.closePath();
  context.fill();
  break;
  case 'diamond':
  const diamondSize = this.size;
  context.beginPath();
  context.moveTo(x, y - diamondSize / 2);
  context.lineTo(x + diamondSize / 2, y);
  context.lineTo(x, y + diamondSize / 2);
  context.lineTo(x - diamondSize / 2, y);
  context.closePath();
  context.fill();
  break;
  case 'ellipse':
  const ellipseWidth = this.size * 2;
  const ellipseHeight = this.size;
  const ellipseX = x - ellipseWidth / 2;
  const ellipseY = y - ellipseHeight / 2;
  context.beginPath();
  context.ellipse(
  x,
  y,
  ellipseWidth / 2,
  ellipseHeight / 2,
  0,
  0,
  2 * Math.PI
  );
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
  window.addEventListener('wheel', this.handleScroll);
  },
  beforeDestroy() {
  window.removeEventListener('wheel', this.handleScroll);
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
  .palettecustom
  {
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
 .palettecustom div {
  width: 4vw;
  height: 6vh;
  cursor: pointer;
  border: 1px solid #000;
  }
  .canvas-container {
    position: relative;
  }

  .canvas-wrapper {
    position: relative;
    z-index: 1; 
  }

  .preview-canvas {
    position: absolute;
    top: 0;
    left: 0;
    pointer-events: none;
    z-index: 2; 
  }
  

  .custom-palette-color button {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: transparent;
    border: none;
    cursor: pointer;
    outline: none;
  }
</style>