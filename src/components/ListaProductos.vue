<template>
  <div ref="box" class="lista" style="max-height: 400px; overflow-y: auto; border: 1px solid #ccc; padding: 10px;">
    <div v-if="cargando">Cargando...</div>
    <div v-else>
      <TarjetaProducto v-for="p in productos" :key="p.id">
        <template #header>
          <h3>{{ p.nombre }} - {{ p.categoria }}</h3>
        </template>
        <template #body="{ expandida, toggleExpandir }">
          <p>Precio: ${{ p.precio }} | Stock: {{ p.stock }}</p>
        </template>
      </TarjetaProducto>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUpdated, onBeforeUnmount, useTemplateRef } from 'vue';
import TarjetaProducto from './TarjetaProducto.vue';

const props = defineProps({
  productos: { type: Array, required: true }
});

const box = useTemplateRef('box');
const cargando = ref(false);
let timer = null;

const esperar = (ms) => new Promise(resolve => setTimeout(resolve, ms));

const cargarProductos = async () => {
  cargando.value = true;
  await esperar(800);
  cargando.value = false;
};

onMounted(() => {
  cargarProductos();
  timer = setInterval(cargarProductos, 30000); // 30 segundos
});

onUpdated(() => {
  if (box.value) {
    box.value.scrollTop = box.value.scrollHeight;
  }
});

onBeforeUnmount(() => {
  clearInterval(timer);
  console.log('ListaProductos desmontado — polling detenido');
});
</script>