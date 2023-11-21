<template>
  <div>
    <canvas ref="canvas" @mousedown="startDrawing" @mousemove="draw" @mouseup="stopDrawing" @mouseleave="stopDrawing"></canvas>
  </div>
</template>

<script>
  export default {
  data() {
  return {
  drawing: false,
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
  draw(event) {
  if (!this.drawing) return;

  const canvas = this.$refs.canvas;
  const context = canvas.getContext('2d');

  const rect = canvas.getBoundingClientRect();
  const x = event.clientX - rect.left;
  const y = event.clientY - rect.top;

  // Set the color to red
  context.fillStyle = 'red';
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
  context.fillStyle = 'red';
  },
  };
</script>

<style scoped="">
  canvas {
  border: 3px solid #000; /* Add a border for better visibility */
  }
</style>
