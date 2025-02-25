<script setup>
import PomodoroTimer from "./components/PomodoroTimer.vue";
</script>

<template>
  <PomodoroTimer />
</template>

<style>
html,
body {
  margin: 0;
  height: 100%;
}

#app {
  height: 100%;
  font-family: "Istok Web";
  letter-spacing: 0.1px;
}

input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

input[type="number"] {
  -moz-appearance: textfield;
}
</style>
