<script setup lang="ts">
import { ref, onMounted } from 'vue';

const canvas = ref<HTMLCanvasElement>();
let isMouseClicked = false;
const size = ref(5);
let color = ref('#000000');
let x1 = 0;
let y1 = 0;
let selectedShape = 'circle';
let isErasing = false;

const DrawCircle = (x: number, y: number) => {
  let ctx = canvas.value?.getContext('2d');
  if (ctx) {
    ctx.beginPath();
    ctx.arc(x, y, size.value, 0, Math.PI * 2);
    ctx.fillStyle = isErasing ? '#ffffff' : color.value;
    ctx.fill();
  }
}

const onMouseDown = (event: MouseEvent) => {
  x1 = event.offsetX;
  y1 = event.offsetY;
  isMouseClicked = true;
}

const onMouseUp = (event: MouseEvent) => {
  x1 = 0;
  y1 = 0;
  isMouseClicked = false;
}
const onMouseMove = (event: MouseEvent) => {
  if (isMouseClicked) {
    if (selectedShape === 'circle') {
      DrawCircle(event.offsetX, event.offsetY);
    } else if(selectedShape==='line') {
      let x2 = event.offsetX;
      let y2 = event.offsetY;
      drawLine(x1, y1, x2, y2);
      x1 = x2;
      y1 = y2;
    }
    else{
      drawRectangle(event.offsetX, event.offsetY);
    }
  }
}

const increaseSize = () => {
  size.value += 5;
  if (size.value > 50) {
    size.value = 50;
  }
}

const decreaseSize = () => {
  size.value -= 5;
  if (size.value < 5) {
    size.value = 5;
  }
}

const handleColorChange = (event: Event) => {
  let eventTarget = event.target as HTMLInputElement;
  color.value = eventTarget.value;
  isErasing = false;
}

const clearCanvas = () => {
  let ctx = canvas.value?.getContext('2d');
  if (ctx) {
    ctx.clearRect(0, 0, canvas.value.width, canvas.value.height);
  }
}

const drawLine = (x1: number, y1: number, x2: number, y2: number) => {
  let ctx = canvas.value?.getContext('2d');
  if (ctx) {
    ctx.beginPath();
    ctx.moveTo(x1, y1);
    ctx.lineTo(x2, y2);
    ctx.strokeStyle = isErasing ? '#ffffff' : color.value;
    ctx.lineWidth = size.value;
    ctx.stroke();
  }
}

const drawRectangle = (x: number, y: number) => {
  let ctx = canvas.value?.getContext('2d');
  if (ctx) {
    ctx.beginPath();
    ctx.rect(x , y , 100, 100);
    ctx.fillStyle = isErasing ? '#ffffff' : color.value;
    ctx.fill();
  }
}
const handleShapeSelection = (shape:string) => {
  selectedShape = shape;
  isErasing = false;
}

const toggleEraser = () => {
  isErasing = true;
}

</script>

<template>
  <div class="container">
    <div class="toolbar">
      <button @click="increaseSize">+</button>
      <span>{{ size }}</span>
      <button @click="decreaseSize">-</button>
      <input type="color" @change="handleColorChange" />
      <button @click="handleShapeSelection('circle')">&#9899;</button>
      <button @click="handleShapeSelection('line')">&#9866;</button>
      <button @click="handleShapeSelection('rectangle')">&#9645;</button>
      <button @click="toggleEraser" class="eraser">&#9635;</button>
      <button @click="clearCanvas" class="clear">&#9746;</button>
    </div>
    <canvas ref="canvas" class="canvas" width="500" height="500" @mousedown="onMouseDown" @mouseup="onMouseUp"
      @mousemove="onMouseMove">
    </canvas>
  </div>
</template>

<style scoped>
:global(body) {
  margin: 0;
  font-family: Arial, sans-serif;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background-color: #f0f0f0;
}

.container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

/* Toolbar styling */
.toolbar {
  width: 500px;
  background-color: #00aaff;
  color: white;
  padding: 10px 20px;
  border-radius: 8px 8px 0 0;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.15);
}

/* Buttons inside toolbar */
.toolbar button {
  background-color: white;
  color: #00aaff;
  border: none;
  padding: 6px 12px;
  font-size: 16px;
  font-weight: bold;
  border-radius: 4px;
  cursor: pointer;
  transition: background 0.2s;
}

.toolbar button:hover {
  background-color: #e0f7ff;
}

.toolbar span {
  font-size: 18px;
  font-weight: bold;
}

.toolbar input[type="color"] {
  width: 40px;
  height: 40px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.toolbar input[type="color"]::-webkit-color-swatch {
  border: none;
  border-radius: 4px;
}

.eraser {
  position: relative;
}

.eraser::after {
  content: "✐";
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 14px;
}

.clear {
  background-color: #ff0000 !important;
  color: white !important;
}

.clear:hover {
  background-color: #ff4d4d !important;
}

/* Canvas styling */
.canvas {
  border: 2px solid #00aaff;
  border-top: none;
  border-radius: 0 0 8px 8px;
  background-color: #ffffff;
}
</style>