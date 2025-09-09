<script setup>
import { ref } from "vue"
// paso 1
const mensaje = ref("esteban")

</script>
<!-- paso 2 -->
<template>
  <div style="font-family: sans-serif; padding: 20px">
    <h2>Two-way Data Binding con v-model</h2>

    <input 
      v-model="mensaje"
      @input="console.log('Valor actual:', mensaje)"
      placeholder="Escribe algo aquí..."
      style="padding: 8px; border:1px solid #ccc; border-radius:4px"
    />

<!-- paso 3 -->
    <p>Lo que escribiste es: <strong>{{ mensaje }}</strong></p>
  </div>
</template>





