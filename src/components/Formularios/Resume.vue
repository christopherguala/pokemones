<template>
  <section>
    <h3>6) Resumen de datos</h3>
    <pre class="code">
{{ {
  nombre,
  descripcion,
  aceptaTerminos,
  skills,
  nivel,
  pais,
  lenguajes
} }}
    </pre>
  </section>
</template>

<script setup>
defineProps([
  'nombre',
  'descripcion',
  'aceptaTerminos',
  'skills',
  'nivel',
  'pais',
  'lenguajes'
]);
</script>

