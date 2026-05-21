[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/otOtREFO)
# TP3 — Ciclo de vida, componentes dinámicos y slots

**Programación Web Dinámica**
**Modalidad:** Individual
**Entrega:** Repositorio en GitHub Classroom

---

## Descripción general

En este trabajo vas a construir una aplicación de catálogo de productos paso a paso. Cada parte agrega una capa encima de la anterior, usando siempre el mismo conjunto de datos.

La estructura final de la aplicación es la siguiente:

```
App.vue
  └── PanelPestanas.vue
        ├── TabTodos.vue
        ├── TabElectronica.vue
        └── TabPerifericos.vue
              └── ListaProductos.vue
                    └── TarjetaProducto.vue
```

---

## Datos compartidos

Creá un archivo `src/data/productos.js` con el siguiente contenido y usalo en todos los componentes que lo necesiten:

```js
export const productos = [
  { id: 1,  nombre: 'Notebook Lenovo IdeaPad',    categoria: 'Electrónica',    precio: 850000, stock: 12 },
  { id: 2,  nombre: 'Monitor Samsung 24"',         categoria: 'Electrónica',    precio: 420000, stock: 8  },
  { id: 3,  nombre: 'Teclado mecánico Redragon',   categoria: 'Periféricos',    precio: 95000,  stock: 25 },
  { id: 4,  nombre: 'Mouse inalámbrico Logitech',  categoria: 'Periféricos',    precio: 48000,  stock: 30 },
  { id: 5,  nombre: 'Auriculares Sony WH-1000',    categoria: 'Audio',          precio: 310000, stock: 5  },
  { id: 6,  nombre: 'Webcam Logitech C920',        categoria: 'Periféricos',    precio: 125000, stock: 15 },
  { id: 7,  nombre: 'SSD Kingston 1TB',            categoria: 'Almacenamiento', precio: 78000,  stock: 40 },
  { id: 8,  nombre: 'Memoria RAM Corsair 16GB',    categoria: 'Componentes',    precio: 62000,  stock: 18 },
  { id: 9,  nombre: 'Parlantes Edifier R1280',     categoria: 'Audio',          precio: 145000, stock: 7  },
  { id: 10, nombre: 'Hub USB-C 7 puertos',         categoria: 'Periféricos',    precio: 35000,  stock: 50 },
]
```

---

## Parte 1 — TarjetaProducto.vue (slots)

Creá un componente `TarjetaProducto.vue` que funcione como contenedor genérico para mostrar un producto. El componente no sabe qué producto va a mostrar — eso lo decide el padre a través de slots.

### Slots nombrados

El componente debe definir tres slots nombrados:

- `#header` — para el nombre y categoría del producto
- `#body` — para el precio y stock
- `#footer` — para acciones (botones, links, etc.)

El slot `#footer` debe tener contenido de fallback que se muestre cuando el padre no lo provee:

```html
<slot name="footer">
  <span>Sin acciones disponibles</span>
</slot>
```

### Slot con scope

El componente debe manejar internamente un estado booleano `expandida` (con `ref`) que indique si la tarjeta está expandida o no, y exponerlo al padre a través del slot `#body`:

```html
<slot name="body" :expandida="expandida" :toggleExpandir="toggleExpandir" />
```

Esto permite que el padre decida qué mostrar según si la tarjeta está expandida o no.

### Uso en el padre

El componente `TarjetaProducto` debe instanciarse al menos tres veces con contenido diferente:

**Instancia 1** — tarjeta completa con los tres slots:

```html
<TarjetaProducto>
  <template #header> ... </template>
  <template #body="{ expandida, toggleExpandir }"> ... </template>
  <template #footer> ... </template>
</TarjetaProducto>
```

**Instancia 2** — tarjeta sin `#footer` para mostrar el contenido fallback:

```html
<TarjetaProducto>
  <template #header> ... </template>
  <template #body="{ expandida, toggleExpandir }"> ... </template>
  <!-- sin #footer → se muestra "Sin acciones disponibles" -->
</TarjetaProducto>
```

**Instancia 3** — libre, con el contenido que quieras.

> **¿Qué tenés que observar?** Cuando no proveés el slot `#footer`, Vue muestra automáticamente el contenido de fallback definido dentro del componente. Cuando sí lo proveés, el fallback se reemplaza por tu contenido.

---

## Parte 2 — ListaProductos.vue (ciclo de vida)

Creá un componente `ListaProductos.vue` que reciba por **prop** un array `productos` y los muestre usando `TarjetaProducto` con `v-for`.

### Props

```js
const props = defineProps({
  productos: {
    type: Array,
    required: true
  }
})
```

### useTemplateRef

Usá `useTemplateRef` para referenciar el `div` contenedor de la lista:

```js
const box = useTemplateRef('box')
```

```html
<div ref="box" class="lista">
  ...
</div>
```

El `div` debe tener `max-height` y `overflow-y: auto` en el CSS para que el scroll sea visible.

### Carga asíncrona simulada

Definí una función auxiliar `esperar(ms)` que retorne una `Promise`:

```js
function esperar(ms) {
  return new Promise(resolve => setTimeout(resolve, ms))
}
```

Definí una función `async cargarProductos()` que use `await esperar(800)` para simular tiempo de red. Mientras carga, mostrá un mensaje `"Cargando..."` usando un `ref` booleano `cargando`.

> **¿Por qué async/await si no hay API?** `async/await` no es exclusivo de llamadas HTTP — es una forma de manejar cualquier operación que tome tiempo. Cuando conectemos la API real en clases siguientes, el único cambio será reemplazar `await esperar(800)` por `await fetch(...)`.

### Hooks de ciclo de vida

**`onMounted`**

- Llamá a `cargarProductos()` al montarse el componente
- Iniciá un `setInterval` que llame a `cargarProductos()` cada 30 segundos y guardá el valor en una variable `timer`

**`onUpdated`**

- Hacé scroll automático al final de la lista usando la template ref:

```js
onUpdated(() => {
  if (box.value) {
    box.value.scrollTop = box.value.scrollHeight
  }
})
```

**`onBeforeUnmount`**

- Limpiá el intervalo para que el polling no siga corriendo cuando el componente ya no existe:

```js
onBeforeUnmount(() => {
  clearInterval(timer)
  console.log('ListaProductos desmontado — polling detenido')
})
```

> **¿Por qué limpiar si los datos son locales?** Ahora no causa problemas porque todo es simulado. Pero cuando `cargarProductos()` haga un `fetch` real, sin el `clearInterval` vas a tener requests disparándose en segundo plano aunque el componente no exista. Es mejor construir el hábito desde el principio.

---

## Parte 3 — Tabs y PanelPestanas.vue (componentes dinámicos)

### Componentes tab

Creá tres componentes en `src/components/tabs/`:

- `TabTodos.vue` — muestra todos los productos
- `TabElectronica.vue` — muestra solo los de categoría `'Electrónica'`
- `TabPerifericos.vue` — muestra solo los de categoría `'Periféricos'`

Cada tab importa el array `productos` desde `src/data/productos.js`, filtra según corresponde, y se lo pasa como prop a `ListaProductos`.

Cada tab debe implementar los cuatro hooks con un `console.log` que lo identifique:

```js
onMounted(()      => console.log('TabTodos — montado'))
onUnmounted(()    => console.log('TabTodos — desmontado'))
onActivated(()    => console.log('TabTodos — activado'))
onDeactivated(()  => console.log('TabTodos — desactivado'))
```

### PanelPestanas.vue

Creá el componente padre que maneje qué tab está activo.

**Sin KeepAlive:**

```html
<component :is="tabActivo" />
```

**Con KeepAlive:**

```html
<KeepAlive>
  <component :is="tabActivo" />
</KeepAlive>
```

Mostrá ambas versiones en la misma página (podés usar un toggle o simplemente mostrarlas una debajo de la otra) para poder comparar el comportamiento en la consola.

Incluí botones para cambiar entre las tres pestañas.

> **¿Qué tenés que observar en la consola?**
>
> - **Sin KeepAlive:** cada vez que cambiás de pestaña, el componente anterior se destruye (`onUnmounted`) y el nuevo se crea desde cero (`onMounted`).
> - **Con KeepAlive:** los componentes se cachean. Al cambiar, se ejecuta `onDeactivated` en el que sale y `onActivated` en el que entra. `onMounted` y `onUnmounted` solo se ejecutan una vez.
>
> Respondé esta pregunta en un comentario dentro de `PanelPestanas.vue`:
> **¿En qué situación conviene usar KeepAlive y en cuál no?**

---

## Estructura de archivos

```
src/
├── data/
│   └── productos.js
├── components/
│   ├── tabs/
│   │   ├── TabTodos.vue
│   │   ├── TabElectronica.vue
│   │   └── TabPerifericos.vue
│   ├── TarjetaProducto.vue
│   └── ListaProductos.vue
├── PanelPestanas.vue
└── App.vue
```

---

## Entrega

1. Hacé push al repositorio asignado por GitHub Classroom
2. El proyecto debe correr con `npm run dev` sin errores en consola (los `console.log` de los hooks no cuentan como errores)
3. Incluí un `README.md` con los pasos para instalar y correr el proyecto

> **Tip:** abrí las DevTools antes de probar el panel de pestañas y dejá la consola visible. La mayoría de lo que se evalúa en la Parte 3 se ve ahí, no en la pantalla.