<template>
  <div class="panel">
    <nav style="margin-bottom: 20px;">
      <button @click="tabActivo = 'TabTodos'">Todos</button>
      <button @click="tabActivo = 'TabElectronica'">Electrónica</button>
      <button @click="tabActivo = 'TabPerifericos'">Periféricos</button>
    </nav>

    <section>
      <h3>Versión SIN KeepAlive (Se destruye al cambiar)</h3>
      <component :is="tabs[tabActivo]" />

      <hr style="margin: 20px 0;" />

      <h3>Versión CON KeepAlive (Se mantiene en memoria)</h3>
      <KeepAlive>
        <component :is="tabs[tabActivo]" />
      </KeepAlive>
    </section>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import TabTodos from './components/Tabs/TabTodos.vue';
import TabElectronica from './components/Tabs/TabElectronica.vue';
import TabPerifericos from './components/Tabs/TabPerifericos.vue';

const tabActivo = ref('TabTodos');
const tabs = { TabTodos, TabElectronica, TabPerifericos };
</script>

<style scoped>
.panel {
  padding: 20px;
  border: 2px solid #333;
}
button {
  margin-right: 10px;
  padding: 10px 20px;
  cursor: pointer;
}
</style>
