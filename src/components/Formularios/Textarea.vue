<template>
  <section>
    <h3>2) Textarea</h3>

    <div class="row">
      <div class="col">
        <label class="label">Two-way (v-model.trim)</label>
        <textarea
          class="control"
          rows="3"
          placeholder="Escribe una descripción"
          :value="descripcion"
          @input="$emit('update:descripcion', $event.target.value.trim())"
          @change="$emit('update:descripcion', $event.target.value.trim())"
        ></textarea>
      </div>
      <div class="col">
        <div class="box info">
          <strong>Descripción:</strong> {{ descripcion }}
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
defineProps(['descripcion']);
defineEmits(['update:descripcion']);
</script>

