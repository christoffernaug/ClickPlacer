<template>
  <div class="buttons">
    <button @click="handleUndo" :disabled="points.length == 0">
      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512">
        <!--! Font Awesome Pro 6.2.1 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2022 Fonticons, Inc. -->
        <path d="M48.5 224H40c-13.3 0-24-10.7-24-24V72c0-9.7 5.8-18.5 14.8-22.2s19.3-1.7 26.2 5.2L98.6 96.6c87.6-86.5 228.7-86.2 315.8 1c87.5 87.5 87.5 229.3 0 316.8s-229.3 87.5-316.8 0c-12.5-12.5-12.5-32.8 0-45.3s32.8-12.5 45.3 0c62.5 62.5 163.8 62.5 226.3 0s62.5-163.8 0-226.3c-62.2-62.2-162.7-62.5-225.3-1L185 183c6.9 6.9 8.9 17.2 5.2 26.2s-12.5 14.8-22.2 14.8H48.5z" />
      </svg>
      Undo
    </button>

    <button id="reset" class="reset" @click="handleReset" :disabled="points.length == 0 && undoedPoints.length == 0">Reset</button>

    <button @click="handleRedo" :disabled="undoedPoints.length == 0">
      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512">
        <!--! Font Awesome Pro 6.2.1 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2022 Fonticons, Inc. -->
        <path d="M463.5 224H472c13.3 0 24-10.7 24-24V72c0-9.7-5.8-18.5-14.8-22.2s-19.3-1.7-26.2 5.2L413.4 96.6c-87.6-86.5-228.7-86.2-315.8 1c-87.5 87.5-87.5 229.3 0 316.8s229.3 87.5 316.8 0c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0c-62.5 62.5-163.8 62.5-226.3 0s-62.5-163.8 0-226.3c62.2-62.2 162.7-62.5 225.3-1L327 183c-6.9 6.9-8.9 17.2-5.2 26.2s12.5 14.8 22.2 14.8H463.5z" />
      </svg>
      Redo
    </button>
  </div>
  <p class="hint">Hit &lt;<span style="color: rgba(255, 255, 255, 0.75); text-decoration: underline">backspace</span>&gt; or button above to reset</p>
  <div class="canvas" @click="handleClick">
    <div v-for="point of points" class="point" :style="{ left: point.x - point.size / 2 + 'px', top: point.y - point.size / 2 + 'px', height: point.size + 'px', width: point.size + 'px', backgroundColor: '#' + point.color }"></div>
    <p class="count" :style="{ color: counterColor }">{{ points.length }}</p>
  </div>
</template>

<script>
import { ref } from "vue";

const points = ref([]);
const undoedPoints = ref([]);
const counterColor = ref("#ffffff80");

export default {
  created() {
    window.addEventListener("keydown", (e) => {
      if (e.key == "Backspace") {
        this.handleReset();
      }
    });
  },
  data() {
    return {
      points,
      undoedPoints,
      counterColor,
    };
  },
  methods: {
    handleUndo: function () {
      const currentPoints = points.value;
      const undoedPoint = currentPoints.pop();

      if (!undoedPoint) return;
      undoedPoints.value.push(undoedPoint);
      points.value = currentPoints;

      counterColor.value = points.value.length > 0 ? "#" + points.value[points.value.length - 1].color : "#ffffff80";
    },
    handleRedo: function () {
      const point = undoedPoints.value.pop();

      if (!point) return;
      points.value.push(point);

      counterColor.value = "#" + point.color;
    },
    handleClick: function (e) {
      const { clientX, clientY } = e;

      const large = Math.random() > 0.75;
      console.log(large);

      const size = Math.floor(Math.random() * ((large ? 400 : 60) - 10) + 10);
      const color = Math.floor(Math.random() * 16777215).toString(16);
      counterColor.value = "#" + color;
      points.value.push({ x: clientX, y: clientY, size: size, color: color });
    },
    handleReset: function () {
      if (points.value.length == 0 && undoedPoints.value.length == 0) return;

      document.getElementById("reset").style.backgroundColor = "rgba(255, 0, 0, 0.5)";
      document.getElementById("reset").style.color = "white";
      setTimeout(() => {
        document.getElementById("reset").style.backgroundColor = "";
        document.getElementById("reset").style.color = "";
      }, 250);

      points.value = [];
      undoedPoints.value = [];
      counterColor.value = "ffffff80";
    },
  },
};
</script>

<style scoped>
.canvas {
  height: 100vh;
}
.point {
  width: 0px;
  height: 0px;
  transition: all 0.25s;
  display: inline-block;
  position: absolute;
  border-radius: 50%;
}

.buttons {
  margin: 15px auto 0 0;
  display: flex;
  justify-content: center;
  align-items: center;
}

button {
  display: inline-flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  flex-grow: 0;

  width: 60px;
  height: 60px;

  border-radius: 50%;
  background-color: #4a4a4a;
  color: white;
  border: 0;

  cursor: pointer;
  margin: 15px;
}

button:disabled {
  background-color: rgba(74, 74, 74, 0.5);
  color: #ffffff80;

  cursor: not-allowed;
}

button svg {
  width: 20px;
  height: 20px;

  fill: white;
  padding: 5px;
}

button:disabled svg {
  fill: rgba(255, 255, 255, 0.5);
}

button.reset {
  width: 70px;
  height: 70px;
  font-size: 20px;
  font-weight: bold;
}

button.reset:enabled {
  box-shadow: 0 0 15px 0 rgba(255, 0, 0, 0.5);
}

button.reset:enabled:hover {
  background-color: rgba(255, 0, 0, 0.5);
}

.hint {
  text-align: center;
  margin: 0;
  color: rgba(255, 255, 255, 0.5);
  text-transform: uppercase;
}

.count {
  position: absolute;
  bottom: 0;
  right: 30px;
  font-size: 30px;
}
</style>
