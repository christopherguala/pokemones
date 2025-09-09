<template>
  <section>
    <h3>1) Input de texto</h3>

    <div class="row">
      <div class="col">
        <label class="label">Two-way (v-model)</label>
        <input
          class="control"
          type="text"
          placeholder="Escribe tu nombre"
          :value="nombre"
          @input="$emit('update:nombre', $event.target.value)"
        />
      </div>
      <div class="col">
        <div class="box info">
          <strong>Vista ⇄ Estado:</strong> {{ nombre }}
        </div>
      </div>
    </div>

    <div class="row mt">
      <div class="col">
        <label class="label">One-way (:value + @input)</label>
        <input
          class="control"
          type="text"
          placeholder="Actualiza manualmente"
          :value="nombreOW"
          @input="$emit('update:nombreOW', $event.target.value)"
        />
      </div>
      <div class="col">
        <div class="box">
          <strong>Solo hacia la vista:</strong> {{ nombreOW }}
        </div>
      </div>
    </div>

    <p class="help">
      Tip: <code>v-model.trim</code> recorta espacios; <code>v-model.number</code> castea a número; <code>v-model.lazy</code> actualiza en <code>change</code>.
    </p>
  </section>
</template>

<script setup>
defineProps(['nombre', 'nombreOW']);
defineEmits(['update:nombre', 'update:nombreOW']);
</script>

