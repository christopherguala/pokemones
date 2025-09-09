<script setup>
//import UserList from './components/userList.vue';
import Formulario from './components/Formulario.vue';
import { ref, computed } from 'vue';
//paso 1 declarar variables
const saludo = 'Hola Mundo'
const styleColor = 'color: blue'
//paso 2 arreglos
const arregloColor = ['red', 'green', 'blue'];
const activo = true;
const frutas = ['manzana', 'pera', 'sandia', 'melón', 'kiwi'];
const objetoFruta = [
  {id: 1, nombre: 'manzana', color: 'red', precio: 100},
  {id: 2, nombre: 'pera', color: 'green', precio: 200},
  {id: 3, nombre: 'sandia', color: 'yellow', precio: 300},
  {id: 4, nombre: 'melón', color: 'orange', precio: 400},
  {id: 5, nombre: 'kiwi', color: 'blue', precio: 500},
]

const objetoFruta2 = {
  id: 1, nombre: 'manzana', color: 'red', precio: 100
}


const handleClick = (mensaje) => {
  console.log(mensaje)
}

const contador = ref(0)

const incrementar = () => {
  contador.value++
}

const decrementar = () => {
  contador.value--
}

const resetear = () => {
  contador.value = 0
}

const classContador = computed(() => {
  if(contador.value === 0) {
return 'zero'
  }
  if(contador.value > 0) {
return 'positivo'
  }
  if(contador.value < 0) {
return 'negativo'
  }
})


</script>

<template>
 <h1>{{ saludo }}</h1>
 <h2 v-bind:style="styleColor">Estilo dinamico</h2>
 <h2>{{ arregloColor[2]}}</h2>
 <h2>{{ activo ? 'activo' : 'inactivo'}}</h2>
 <!-- DIRECTIVAS -->

 <!-- v-if -->
 <h2 v-if="activo">activo</h2>
 <h2 v-else>inactivo</h2>

 <!-- v-show -->
 <h2 v-show="activo">activo</h2>
 <h2 v-show="!activo">inactivo</h2>

 <!-- v-for -->
 <ul>
  <li v-for="(fruta, index) in frutas" :key="index">
    {{index}}-{{ fruta }}
  </li>
 </ul>

 <!-- v-for con array de objetos -->
  <ul v-for="(fruta, index) in objetoFruta" :key="index">
<li>{{ index }}-{{ fruta.nombre }}-{{ fruta.precio }}</li>
  </ul>

  <!-- v-for con objeto -->
  <ul>
    <li v-for="(value, index) in objetoFruta2" :key="index">
      {{ index }}-{{ value }}
    </li>
  </ul>

  <!-- EVENTOS -->
<h2>EVENTOS</h2>

<!-- <button @click="handleClick('hola mundo')">Alerta</button> -->
   <!-- MODIFICADORES DE EVENTOS -->
<button @click.left="handleClick('hola mundo')">click izquierdo</button>
<button @click.right.prevent="handleClick('hola mundo')">click derecho</button>
<button @click.middle.prevent="handleClick('hola mundo')">click central</button>
<hr>
<!-- reactividad -->
  <h2>REACTIVIDAD</h2>
  <button @click="incrementar">Incrementar</button>
  <button @click="decrementar">Decrementar</button>
  <button @click="resetear">Resetear</button>
  <!-- <h2 :class="contador > 0 ? 'positivo' : 'negativo'"> {{contador}}</h2> -->
  <h2 :class="classContador"> {{ contador }}</h2>


<hr>
<Formulario />
</template>

<style>
h1 {
  color: red
}

.positivo {
  color: green
}

.negativo {
  color: red
}

.zero {
  color: blue
}
</style>