<script setup lang="ts">
import SerieList from '@/components/serie/SerieList.vue'
</script>

<template>
  <div class="contenedor">
    <h1>Series</h1>
    <SerieList />
  </div>
</template>

<style scoped>
.contenedor {
  border: 2px solid white;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}
</style>
