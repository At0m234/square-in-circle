<template>
  <div>
    <h1>Квадрат в окружности</h1>
    <div class="controls">
      <div class="control">
        <label class="label" for="radiusInput">Радиус окружности:</label>
        <input class="input" type="number" id="radiusInput" v-model="radius" @input="drawCircleSquare" />
      </div>
      <div class="control">
        <label class="label" for="sizeInput">Размер стороны квадрата:</label>
        <input class="input" type="number" id="sizeInput" v-model="squareSize" @input="drawCircleSquare" />
      </div>
    </div>

    <div class="controls">
      <div class="control">
        <label class="label" for="xInput">X координата квадрата:</label>
        <input class="input" type="number" id="xInput" v-model="squareX" @input="drawCircleSquare" />
      </div>
      <div class="control">
        <label class="label" for="yInput">Y координата квадрата:</label>
        <input class="input" type="number" id="yInput" v-model="squareY" @input="drawCircleSquare" />
      </div>
    </div>
    <canvas class="canvas" ref="canvas" @mousemove="handleMouseMove" @mousedown="handleMouseDown"
      @mouseup="handleMouseUp">
    </canvas>
  </div>
</template>

<script setup>
import { ref, onMounted, reactive } from 'vue';

const radius = ref(50);
const squareSize = ref(20);
const squareX = ref(15);
const squareY = ref(15);
const canvas = ref(null);
const isDragging = ref(false);
const offset = reactive({ x: 0, y: 0 });

function drawCircleSquare() {
  const context = canvas.value.getContext("2d");
  context.clearRect(0, 0, canvas.value.width, canvas.value.height);

  context.beginPath();
  context.arc(canvas.value.width / 2, canvas.value.height / 2, radius.value, 0, 2 * Math.PI);
  context.fillStyle = 'blue';
  context.fill();

  const halfSquareSize = squareSize.value / 2;
  const squareXPos = canvas.value.width / 2 + squareX.value - halfSquareSize;
  const squareYPos = canvas.value.height / 2 + squareY.value - halfSquareSize;

  context.fillStyle = 'red'; 
  context.fillRect(squareXPos, squareYPos, squareSize.value, squareSize.value);
}


function handleMouseDown(event) {
  const rect = canvas.value.getBoundingClientRect();
  const mouseX = event.clientX - rect.left;
  const mouseY = event.clientY - rect.top;

  // Проверяем, было ли нажатие на квадрат
  const halfSquareSize = squareSize.value / 2;
  const squareXPos = canvas.value.width / 2 + squareX.value - halfSquareSize;
  const squareYPos = canvas.value.height / 2 + squareY.value - halfSquareSize;

  if (
    mouseX >= squareXPos &&
    mouseX <= squareXPos + squareSize.value &&
    mouseY >= squareYPos &&
    mouseY <= squareYPos + squareSize.value
  ) {
    isDragging.value = true;
    offset.x = mouseX - squareX.value;
    offset.y = mouseY - squareY.value;
  }
}

function handleMouseMove(event) {
  if (isDragging.value) {
    const rect = canvas.value.getBoundingClientRect();
    const mouseX = event.clientX - rect.left;
    const mouseY = event.clientY - rect.top;

    const halfSquareSize = squareSize.value / 2;

    // Вычисляем расстояние от каждой стороны квадрата до центра окружности
    const squareLeft = canvas.value.width / 2 + squareX.value - halfSquareSize;
    const squareRight = squareLeft + squareSize.value;
    const squareTop = canvas.value.height / 2 + squareY.value - halfSquareSize;
    const squareBottom = squareTop + squareSize.value;

    // Вычисляем центр окружности
    const circleCenterX = canvas.value.width / 2;
    const circleCenterY = canvas.value.height / 2;

    // Вычисляем расстояние от каждой стороны квадрата до центра окружности
    const distanceLeft = Math.abs(circleCenterX - squareLeft);
    const distanceRight = Math.abs(circleCenterX - squareRight);
    const distanceTop = Math.abs(circleCenterY - squareTop);
    const distanceBottom = Math.abs(circleCenterY - squareBottom);

    // Разрешаем перемещение, если хотя бы одна сторона квадрата находится внутри окружности
    if (
      distanceLeft <= radius.value &&
      distanceRight <= radius.value &&
      distanceTop <= radius.value &&
      distanceBottom <= radius.value
    ) {
      squareX.value = mouseX - offset.x;
      squareY.value = mouseY - offset.y;
    }

    drawCircleSquare();
  }
}

function handleMouseUp() {
  isDragging.value = false;
}

onMounted(() => {
  // Устанавливаем размеры холста и вызываем функцию для отображения круга с квадратом
  canvas.value.width = canvas.value.offsetWidth;
  canvas.value.height = canvas.value.offsetHeight;
  drawCircleSquare();
});
</script>


<style scoped>
.controls {
  display: flex;
  justify-content: space-evenly;
  align-items: center;
  margin-bottom: 20px;
  flex-wrap: wrap;
}

.control {
  display: flex;
  justify-content: space-between;
  padding: 10px;
  width: 450px;
}

.label {
  font-size: 20px;
  margin: 10px;
}

.input {
  width: 30%;
  padding: 10px;
  border: 1px solid #fff;
  border-radius: 10px;
  font-size: 20px;
}

.canvas {
  width: 100%;
  height: 85vh;
  border: 1px solid #000;
  background-color: #F8F8FF;
}
</style>
