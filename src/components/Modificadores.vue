<script setup>
import { ref } from "vue";

const nombre = ref("");

function padreClick() {
  alert("Se hizo clic en el DIV padre");
}

function hijoClick() {
  alert("Se hizo clic en el BOTÓN hijo");
}

function enviarFormulario() {
  alert(`Formulario enviado por: ${nombre.value}`);
}

function saludoUnico() {
  alert("¡Hola! Este mensaje aparece solo una vez 😉");
}

function soloDiv() {
  alert("Clic directo en el DIV verde");
}

function capturaPadre() {
  console.log("Evento capturado primero en el PADRE (fase de captura)");
}

function capturaHijo() {
  console.log("Evento del HIJO en la fase normal (burbuja)");
}
</script>

<template>
  <section>
    <h2>Modificadores de eventos</h2>

    <!-- .stop evita que el evento siga propagándose -->
    <div @click="padreClick" style="padding: 20px; background: lightblue;">
      <p>Div Padre (clic aquí también se activa con el botón hijo si no usamos .stop)</p>

      <button @click.stop="hijoClick">Botón Hijo con .stop</button>
    </div>

    <!-- .prevent evita el comportamiento por defecto (ejemplo: recargar al enviar un form) -->
    <form @submit.prevent="enviarFormulario" style="margin-top: 20px;">
      <input v-model="nombre" placeholder="Escribe tu nombre" />
      <button type="submit">Enviar</button>
    </form>

    <!-- .once hace que el evento solo se ejecute la primera vez -->
    <button @click.once="saludoUnico" style="margin-top: 20px;">
      Saludo con .once
    </button>

    <!-- .self se dispara solo si haces clic directamente en el div (no en elementos hijos) -->
    <div
      @click.self="soloDiv"
      style="margin-top: 20px; padding: 20px; background: lightgreen;"
    >
      <p>Haz clic dentro, pero si haces clic en el texto no se activa (porque no es el div)</p>
    </div>

    <!-- .capture hace que el evento se capture en la fase de captura (antes que los hijos) -->
    <div @click.capture="capturaPadre" style="margin-top: 20px; padding: 20px; background: lightcoral;">
      <button @click="capturaHijo">Hijo con evento normal</button>
    </div>
 
  </section>
</template>

