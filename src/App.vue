<template>
  <!-- <div>
    <a href="https://vitejs.dev" target="_blank">
      <img src="/vite.svg" class="logo" alt="Vite logo" />
    </a>
    <a href="https://vuejs.org/" target="_blank">
      <img src="./assets/vue.svg" class="logo vue" alt="Vue logo" />
    </a>
  </div>
  <HelloWorld msg="Vite + Vue" /> -->
  <ThreeJS />
</template>

<script setup>
import HelloWorld from './components/HelloWorld.vue'
// import ThreeJS from './components/ThreeJsMap.vue'
// import ThreeJS from './components/threeTest.vue'
// import ThreeJS from './components/ThreeBarChart.vue'
// import ThreeJS from './components/ThreeCuboidBarChart.vue';
// import ThreeJS from './components/ThreeCurveChart.vue';
import ThreeJS from './components/ThreeRingChart.vue'


</script>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}

.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}

.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
