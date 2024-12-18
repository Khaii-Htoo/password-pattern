<template>
  <div id="app">
    <div class="lock-container">
    <h2>Pattern Lock Screen</h2>
    <canvas
      ref="patternCanvas"
      width="300"
      height="300"
      @mousedown="startPattern"
      @mousemove="drawPattern"
      @mouseup="endPattern"
      @mouseleave="endPattern"
    ></canvas>
    <p>{{ message }}</p>
  </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      points: [],
      selectedPoints: [],
      isDrawing: false,
      ctx: null,
      message: "Draw your pattern",
    };
  },
  mounted() {
    const canvas = this.$refs.patternCanvas;
    this.ctx = canvas.getContext("2d");
    this.initializePoints();
    this.drawPoints();
  },
  methods: {
    // Initialize grid points
    initializePoints() {
      this.points = [];
      const offset = 60; // Distance between points
      const radius = 10;
      for (let i = 0; i < 3; i++) {
        for (let j = 0; j < 3; j++) {
          this.points.push({
            x: j * offset + 80,
            y: i * offset + 80,
            radius,
            selected: false,
          });
        }
      }
    },
    // Draw static points on canvas
    drawPoints() {
      this.ctx.clearRect(0, 0, 300, 300);
      this.points.forEach((point) => {
        this.ctx.beginPath();
        this.ctx.arc(point.x, point.y, point.radius, 0, Math.PI * 2);
        this.ctx.fillStyle = point.selected ? "#4caf50" : "#aaa";
        this.ctx.fill();
      });
    },
    // Start drawing the pattern
    startPattern(event) {
      this.isDrawing = true;
      this.selectedPoints = [];
      this.updatePattern(event);
    },
    // Update pattern based on mouse position
    drawPattern(event) {
      if (!this.isDrawing) return;
      this.updatePattern(event);
    },
    updatePattern(event) {
      const { offsetX, offsetY } = event;
      this.points.forEach((point) => {
        const distance = Math.hypot(point.x - offsetX, point.y - offsetY);
        if (distance < point.radius * 2 && !point.selected) {
          point.selected = true;
          this.selectedPoints.push(point);
          this.connectPoints();
        }
      });
    },
    // Connect selected points with lines
    connectPoints() {
      this.drawPoints();
      this.ctx.beginPath();
      this.ctx.strokeStyle = "#4caf50";
      this.ctx.lineWidth = 3;
      this.ctx.moveTo(this.selectedPoints[0].x, this.selectedPoints[0].y);
      this.selectedPoints.forEach((point) => {
        this.ctx.lineTo(point.x, point.y);
      });
      this.ctx.stroke();
    },
    // End the pattern drawing
    endPattern() {
      if (this.isDrawing) {
        this.isDrawing = false;
        this.message = `Pattern: ${this.selectedPoints
          .map((_, index) => index + 1)
          .join("-")}`;
        setTimeout(this.resetPattern, 1500);
      }
    },
    // Reset pattern
    resetPattern() {
      this.points.forEach((point) => (point.selected = false));
      this.selectedPoints = [];
      this.message = "Draw your pattern";
      this.drawPoints();
    },
  },
};
</script>

<style scoped>
#app {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #222;
  margin: 0;
}
.lock-container {
  text-align: center;
  margin-top: 50px;
}

canvas {
  background-color: #333;
  border: 2px solid #444;
  border-radius: 10px;
  cursor: pointer;
}
p {
  margin-top: 20px;
  font-size: 18px;
  color: #4caf50;
}
</style>