<!-- v2 option api -->
<!-- <template>
  <button @click="saludar">Haz clic</button>
  <hr>
</template>

<script>
export default {
  data() {
    return {
      nombre: "Esteban"
    };
  },
  methods: {
    saludar() {
      alert(`Hola ${this.nombre}`);
    }
  }
};
</script> -->

<!-- vue3 api composition-->
<template>
  <button @click="saludar">Haz clic</button>
</template>

<script setup>
import { ref } from "vue";

const nombre = ref("Esteban");

function saludar() {
  alert(`Hola ${nombre.value}`);
}
</script>

