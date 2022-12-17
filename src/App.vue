<script setup lang="ts"></script>

<template>
  <div class="app">
    <RouterView />
  </div>
</template>

<style scoped>
  .app {
    display: flex;
    flex-direction: column;
    width: 100%;
    max-width: 1024px;
    align-self: center;
    min-height: 100%;
    padding-bottom: 24px;
  }
</style>
