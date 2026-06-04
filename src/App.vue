<template>
  <main>
    <h1>Catálogo de Productos</h1>
    <PanelPestanas />
  </main>
</template>

<script setup>
import PanelPestanas from './PanelPestanas.vue';
</script>

<style scoped>
main {
  max-width: 1000px; /* Aumenta este número para que sea más ancho */
  margin: 0 auto;
  padding: 20px;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.lista {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 15px;
}
</style>