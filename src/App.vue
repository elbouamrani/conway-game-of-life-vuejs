<template>
  <div id="app">
    <span>size: {{ size }} | </span>
    <span>cycle: {{ cycle }} | </span>
    <button @click="toggle">
      {{ running ? "stop" : "start" }}
    </button>
    <button @click="resetMap">reset</button>
    <hr />
    <div>
      <div v-for="(row, i) in map" :key="i" class="row">
        <div
          v-for="(column, j) in map"
          :key="j"
          class="cell"
          :style="{
            backgroundColor: map[i][j] ? 'black' : 'transparent',
          }"
          @click="toggleSquare(i, j)"
        ></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      map: [],
      size: 20,
      running: false,
      cycle: 0,
    };
  },
  methods: {
    toggleSquare(i, j) {
      let map = [...this.map];
      map[i][j] = map[i][j] ? 0 : 1;
      this.map = map;
    },
    updateMap() {
      const map = [];

      for (let i = 0; i < this.size; i++) {
        let row = [];
        for (let j = 0; j < this.size; j++) {
          const judjement = this.evaluateCell(i, j, this.map[i][j]);
          row.push(judjement);
        }
        map.push(row);
      }
      this.map = map;
      this.cycle++;
    },
    resetMap() {
      this.initMap();
      this.cycle = 0;
      this.stop();
    },
    toggle() {
      if (this.running) {
        this.stop();
      } else {
        this.start();
      }
    },
    start() {
      this.running = true;
    },
    stop() {
      this.running = false;
    },
    evaluateCell(i, j, status = 0) {
      let alive = 0;

      alive += this.getCellValue(i + 1, j - 1);
      alive += this.getCellValue(i + 1, j);
      alive += this.getCellValue(i + 1, j + 1);

      alive += this.getCellValue(i - 1, j - 1);
      alive += this.getCellValue(i - 1, j);
      alive += this.getCellValue(i - 1, j + 1);

      alive += this.getCellValue(i, j - 1);
      alive += this.getCellValue(i, j + 1);

      //if current cell is alive
      if (status) {
        if (alive === 2 || alive === 3) {
          return 1;
        } else {
          //die if underpopulation (<2) or overpopulation (> 3)
          return 0;
        }
      } else {
        if (alive === 3) {
          return 1;
        }
      }
    },
    getCellValue(i, j) {
      try {
        if (this.map[i][j] === 1) {
          return 1;
        } else {
          return 0;
        }
      } catch (error) {
        return 0;
      }
    },
    initMap() {
      const map = [];
      for (let i = 0; i < this.size; i++) {
        let row = [];
        for (let j = 0; j < this.size; j++) {
          // const rand = Math.round(Math.random());
          // row.push(rand);
          row.push(0);
        }
        map.push(row);
      }
      this.map = map;
    },
  },
  mounted() {
    this.initMap();
    setInterval(() => {
      if (this.running) {
        this.updateMap();
      }
    }, 1000);
  },
};
</script>

<style>
.row {
  display: flex;
}
.cell {
  width: 10pt;
  height: 10pt;
  border: 0.5pt solid black;
  cursor: pointer;
}
</style>

