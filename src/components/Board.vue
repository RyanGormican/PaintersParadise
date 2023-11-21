<template>

</template>

<script>
  export default {
  data() {
  return {
  drawing: false,
  selectedColor: 'red',
  paletteColors: ['#ff0000','#ffae00','#ffff5c','#00ff00', '#0000ff','#3f0fff'],
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
  selectColor(color){
  this.selectedColor=color;
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
  canvas.style.margin = 'auto';
  canvas.style.display = 'block';

  // Set initial color
  context.fillStyle = this.selectedColor;
  },
  };
</script>

<style scoped="">
  canvas {
  border: 3px solid #000; 
  }
  .palette {
  display: flex;
  margin:auto;
  justify-content:center;
  }
  .palette div {
  width: 4vw;
  height:4vh;
  cursor:pointer;
  border: 1px solid #000;
  }
</style>
