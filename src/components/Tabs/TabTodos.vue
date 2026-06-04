<template>
  <ListaProductos :productos="productos" />
</template>

<script setup>
import { onMounted, onUnmounted, onActivated, onDeactivated } from 'vue';
import { productos } from '../../data/productos.js';
import ListaProductos from '../ListaProductos.vue';

// Hooks de ciclo de vida solicitados
onMounted(() => console.log('TabTodos — montado'));
onUnmounted(() => console.log('TabTodos — desmontado'));
onActivated(() => console.log('TabTodos — activado'));
onDeactivated(() => console.log('TabTodos — desactivado'));
</script>