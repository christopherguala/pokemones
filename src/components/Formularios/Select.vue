<template>
  <section>
    <h3>5) Select</h3>

    <div class="row">
      <!-- Single -->
      <div class="col">
        <label class="label">Select (single)</label>
        <select
          class="control"
          :value="pais"
          @change="$emit('update:pais', $event.target.value)"
        >
          <option disabled value="">Selecciona un país...</option>
          <option>Chile</option>
          <option>Argentina</option>
          <option>Perú</option>
        </select>
        <p class="help">País: {{ pais || '—' }}</p>
      </div>

      <!-- Multiple -->
      <div class="col">
        <label class="label">Select (multiple → array)</label>
        <select
          class="control"
          multiple
          size="3"
          :value="lenguajes"
          @change="onChangeMultiple"
        >
          <option value="js">JavaScript</option>
          <option value="py">Python</option>
          <option value="go">Go</option>
        </select>
        <p class="help">Lenguajes: {{ (lenguajes && lenguajes.length) ? lenguajes.join(', ') : '—' }}</p>
      </div>
    </div>
  </section>
</template>

<script setup>
const props = defineProps(['pais', 'lenguajes']);
const emit = defineEmits(['update:pais', 'update:lenguajes']);

function onChangeMultiple(e) {
  const values = Array.from(e.target.selectedOptions).map(o => o.value);
  emit('update:lenguajes', values);
}
</script>

