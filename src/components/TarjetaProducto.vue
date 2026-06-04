<template>
  <div class="tarjeta">
    <slot name="header"></slot>

    <div v-if="expandida" class="body">
      <slot name="body" :expandida="expandida" :toggleExpandir="toggleExpandir"></slot>
    </div>

    <footer>
      <slot name="footer">
        <span>Sin acciones disponibles</span>
      </slot>
    </footer>

    <button @click="toggleExpandir">
      {{ expandida ? 'Ocultar' : 'Ver detalle' }}
    </button>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const expandida = ref(false);
const toggleExpandir = () => {
  expandida.value = !expandida.value;
};
</script>

<style scoped>
.tarjeta {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 15px;
  margin: 10px 0;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}
button {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 8px 12px;
  border-radius: 4px;
  cursor: pointer;
}
</style>