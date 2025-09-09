<template>
  <section>
    <h3>4) Radios (grupo)</h3>

    <label class="check">
      <input
        type="radio"
        value="Básico"
        :checked="nivel === 'Básico'"
        @change="$emit('update:nivel', 'Básico')"
      />
      <span>Básico</span>
    </label>

    <label class="check">
      <input
        type="radio"
        value="Intermedio"
        :checked="nivel === 'Intermedio'"
        @change="$emit('update:nivel', 'Intermedio')"
      />
      <span>Intermedio</span>
    </label>

    <label class="check">
      <input
        type="radio"
        value="Avanzado"
        :checked="nivel === 'Avanzado'"
        @change="$emit('update:nivel', 'Avanzado')"
      />
      <span>Avanzado</span>
    </label>

    <div class="box info mt">
      <strong>Nivel elegido:</strong> {{ nivel }}
    </div>
  </section>
</template>

<script setup>
defineProps(['nivel']);
defineEmits(['update:nivel']);
</script>
