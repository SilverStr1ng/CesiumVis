<template>
  <div class="map-container">
    <CesiumMap />
  </div>
</template>

<script setup lang="ts">
  import CesiumMap from "@/components/CesiumMap.vue";
</script>

<style scoped>
  .map-container {
    width: 100%;
    height: 100vh;
  }
</style>
