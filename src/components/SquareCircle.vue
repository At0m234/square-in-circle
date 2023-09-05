<template>
  <div>
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

<script setup lang="ts">
import { ref, onMounted, reactive, Ref } from 'vue';

interface Coordinates {
  x: number;
  y: number;
}

const radius: Ref<number> = ref(400);
const squareSize: Ref<number> = ref(80);
const squareX: Ref<number> = ref(15);
const squareY: Ref<number> = ref(15);
const canvas: Ref<null | HTMLCanvasElement> = ref(null);
const isDragging: Ref<boolean> = ref(false);
const offset: Coordinates = reactive({ x: 0, y: 0 });

function drawCircleSquare(): void {
  const context = canvas.value?.getContext("2d");
  if (!context || !canvas.value) return;

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

function handleMouseDown(event: MouseEvent): void {
  if (!canvas.value) return;
  const rect = canvas.value?.getBoundingClientRect();
  if (!rect) return;

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

function handleMouseMove(event: MouseEvent): void {
  if (!canvas.value) return;
  if (isDragging.value) {
    const rect = canvas.value?.getBoundingClientRect();
    if (!rect) return;

    const mouseX = event.clientX - rect.left;
    const mouseY = event.clientY - rect.top;

    // Ограничиваем перемещение квадрата внутри окружности
    const centerX = canvas.value.width / 2;
    const centerY = canvas.value.height / 2;
    const distanceToCenter = Math.sqrt(Math.pow(mouseX - centerX, 2) + Math.pow(mouseY - centerY, 2));

    // Рассчитываем расстояния от углов квадрата до центра окружности
    const halfSquareSize = squareSize.value / 2;
    const topLeftDistance = Math.sqrt(Math.pow(mouseX - (centerX - halfSquareSize), 2) + Math.pow(mouseY - (centerY - halfSquareSize), 2));
    const topRightDistance = Math.sqrt(Math.pow(mouseX - (centerX + halfSquareSize), 2) + Math.pow(mouseY - (centerY - halfSquareSize), 2));
    const bottomLeftDistance = Math.sqrt(Math.pow(mouseX - (centerX - halfSquareSize), 2) + Math.pow(mouseY - (centerY + halfSquareSize), 2));
    const bottomRightDistance = Math.sqrt(Math.pow(mouseX - (centerX + halfSquareSize), 2) + Math.pow(mouseY - (centerY + halfSquareSize), 2));

    // Проверяем, что центр квадрата все углы находятся внутри окружности
    if (
      distanceToCenter <= radius.value - halfSquareSize &&
      topLeftDistance <= radius.value &&
      topRightDistance <= radius.value &&
      bottomLeftDistance <= radius.value &&
      bottomRightDistance <= radius.value
    ) {
      squareX.value = mouseX - offset.x;
      squareY.value = mouseY - offset.y;
    }

    drawCircleSquare();
  }
}

function handleMouseUp(): void {
  isDragging.value = false;
}

onMounted(() => {
  if (canvas.value) {
    canvas.value.width = canvas.value.offsetWidth;
    canvas.value.height = canvas.value.offsetHeight;
    drawCircleSquare();
  }
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
