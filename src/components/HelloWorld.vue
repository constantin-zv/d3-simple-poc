<script setup lang="ts">
import { ref, reactive, onMounted } from 'vue';
import * as d3 from 'd3';

const width = 400;
const height = 500;
const rectH = 40;
const rectW = 80;

const myData = [
  { name: 'card1' },
  { name: 'card2' },
  { name: 'card3' },
  { name: 'card4' },
];

const arcs = new Array(myData.length - 1);

const scaleX = d3.scaleLinear().domain([0, 100]).range([0, width]);
const scaleY = d3.scaleLinear().domain([0, 100]).range([height, 0]);

const getRectX = () => scaleX(50) - rectW / 2; // center rect
const getRectY = (index) => scaleY(rectH / 2 + index * (rectH / 2));

// Zooming related:
const transformation = reactive({
  x: 0,
  y: 0,
  scale: 1,
});

const zoomFn = d3.zoom().on('zoom', (event) => {
  const { x, y, k } = event.transform;
  transformation.x = x;
  transformation.y = y;
  transformation.scale = k;
});

onMounted(() => {
  d3.select('svg').call(zoomFn);
});
</script>

<template>
  <div>My data</div>
  <svg :width="width" :height="height">
    <g
      :transform="`translate(${transformation.x}, ${transformation.y}) scale(${transformation.scale})`"
    >
      <line
        v-for="(dataEntry, index) in arcs"
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
    </g>
  </svg>
</template>

<style scoped></style>
