<script setup lang="ts">
import { reactive, onMounted } from 'vue';
import { scaleLinear, zoom, select } from 'd3';

const width = 600;
const height = 800;
const rectH = 40;
const rectW = 80;

const myData = [
  { name: 'card1' },
  { name: 'card2' },
  { name: 'card3' },
  { name: 'card4' },
];

const arcs = new Array(myData.length - 1);

const scaleX = scaleLinear().domain([0, 100]).range([0, width]);
const scaleY = scaleLinear().domain([0, 100]).range([0, height]);

const getRectX = () => scaleX(50) - rectW / 2; // center rect
const getRectY = (index: number) => scaleY(20 +index * (rectH / 2));

// Zooming related:
const transformation = reactive({
  x: 0,
  y: 0,
  scale: 1,
});

const zoomFn = zoom().on('zoom', (event) => {
  const { x, y, k } = event.transform;
  transformation.x = x;
  transformation.y = y;
  transformation.scale = k;
});

onMounted(() => {
  select('svg').call(zoomFn);
});
</script>

<template>
  <svg :width="width" :height="height" style="border: 1px solid green;">
    <g
      :transform="`translate(${transformation.x}, ${transformation.y}) scale(${transformation.scale})`"
    >
      <line
        v-for="(_, index) in arcs"
        :key="index"
        :x1="scaleX(50)"
        :y1="getRectY(index)"
        :x2="scaleX(50)"
        :y2="getRectY(index + 1) + rectH"
        stroke="steelblue"
      />
      <rect
        v-for="(dataEntry, index) in myData"
        :key="dataEntry.name"
        :width="rectW"
        :height="rectH"
        fill="red"
        :x="getRectX()"
        :y="getRectY(index)"
      />

      <text
        v-for="(dataEntry, index) in myData"
        :key="dataEntry.name"
        :x="getRectX() + rectW / 4"
        :y="getRectY(index) + rectH / 2"
        fill="white"
      > {{ dataEntry.name }}
      </text>
    </g>
  </svg>
</template>

<style scoped></style>
