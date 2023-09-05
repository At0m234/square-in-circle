<template>
  <div>
    <h1>Квадрат в окружности</h1>
    <div class="controls">
      <label class="label" for="radiusInput">Радиус окружности:</label>
      <input class="input" type="number" id="radiusInput" v-model="radius" @input="drawCircleSquare" />

      <label class="label" for="sizeInput">Размер стороны квадрата:</label>
      <input class="input" type="number" id="sizeInput" v-model="squareSize" @input="drawCircleSquare" />

      <label class="label" for="xInput">X координата квадрата:</label>
      <input class="input" type="number" id="xInput" v-model="squareX" @input="drawCircleSquare" />

      <label class="label" for="yInput">Y координата квадрата:</label>
      <input class="input" type="number" id="yInput" v-model="squareY" @input="drawCircleSquare" />
    </div>
    <canvas class="canvas" ref="canvas"></canvas>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

const radius = ref(50);
const squareSize = ref(20);
const squareX = ref(100);
const squareY = ref(100);
const canvas = ref(null);

function drawCircleSquare() {
  const context = canvas.value.getContext("2d");
  context.clearRect(0, 0, canvas.value.width, canvas.value.height);
  context.beginPath();
  context.arc(canvas.value.width / 2, canvas.value.height / 2, radius.value, 0, 2 * Math.PI);
  context.stroke();
  const halfSquareSize = squareSize.value / 2;
  const squareXPos = canvas.value.width / 2 + squareX.value - halfSquareSize;
  const squareYPos = canvas.value.height / 2 + squareY.value - halfSquareSize;
  context.fillRect(squareXPos, squareYPos, squareSize.value, squareSize.value);
}

</script>

<style scoped>
.controls {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
}

.label {
  font-size: 20px;
  margin: 10px 0;
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
  border: 1px solid #fff;
}
</style>
