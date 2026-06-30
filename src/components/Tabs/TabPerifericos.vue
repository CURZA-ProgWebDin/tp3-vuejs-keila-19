<template>
  <ListaProductos :productos="productosFiltrados" />
</template>

<script setup>
import { computed, onMounted, onUnmounted, onActivated, onDeactivated } from 'vue';
import { productos } from '../../data/productos.js';
import ListaProductos from '../ListaProductos.vue';

// Filtramos solo los productos que pertenecen a la categoría 'Periféricos'
const productosFiltrados = computed(() => productos.filter(p => p.categoria === 'Periféricos'));

onMounted(() => console.log('TabPerifericos — montado'));
onUnmounted(() => console.log('TabPerifericos — desmontado'));
onActivated(() => console.log('TabPerifericos — activado'));
onDeactivated(() => console.log('TabPerifericos — desactivado'));
</script>