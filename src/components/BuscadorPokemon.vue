
<script setup>
import { ref, computed, onMounted } from "vue";

/** Estado principal */
const pokemones = ref([]);        // { id, nombre }
const loading = ref(true);
const loadError = ref("");

/** Estado del formulario */
const query = ref("");
const touched = ref(false);
const errorMsg = ref("");

/** Handlers */
function onSubmit() {
  touched.value = true;
  validate(); // solo muestra error; el filtrado es reactivo
}
function onInput() {
  if (touched.value) validate();
}
function limpiar() {
  query.value = "";
  touched.value = false;
  errorMsg.value = "";
}

/** Derivados */
const showError = computed(() => touched.value && !!errorMsg.value);

const filtered = computed(() => {
  const q = query.value.trim().toLowerCase();
  if (q === "") return pokemones.value;
  return pokemones.value.filter((p) => p.nombre.toLowerCase().includes(q));
});

/** Carga desde PokeAPI */
async function cargarPokemones(limit = 151, offset = 0) {
  loading.value = true;
  loadError.value = "";
  try {
    const res = await fetch(
      `https://pokeapi.co/api/v2/pokemon?limit=${limit}&offset=${offset}`
    );
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    const data = await res.json();

    // Mapeamos a { id, nombre } usando el id presente en la URL
    pokemones.value = (data.results || []).map((item) => {
      // URL típica: https://pokeapi.co/api/v2/pokemon/25/
      const parts = item.url.split("/").filter(Boolean);
      const id = parts[parts.length - 1]; // penúltimo segmento real
      return { id, nombre: item.name, url: item.url };
    });
  } catch (err) {
    loadError.value =
      "No se pudo cargar la lista desde PokeAPI. Intenta nuevamente.";
    console.error(err);
  } finally {
    loading.value = false;
  }
}

onMounted(() => {
  // Carga inicial: los 151 de Kanto (se puede subir a 300)
  cargarPokemones(151, 0);
});
</script>

<template>
  <section class="container py-4">
    <h2 class="mb-3">Buscador de Pokémon</h2>

    <!-- Estados de carga / error de red -->
    <div v-if="loading" class="alert alert-info">Cargando Pokémon...</div>
    <div v-else-if="loadError" class="alert alert-danger d-flex justify-content-between align-items-center">
      <span>{{ loadError }}</span>
      <button class="btn btn-sm btn-outline-light" @click="cargarPokemones()">Reintentar</button>
    </div>

    <form v-if="!loading && !loadError" novalidate @submit.prevent="onSubmit" class="mb-3">
      <div class="row g-2 align-items-start">
        <div class="col-sm-8 col-md-6">
          <label class="form-label" for="query">Nombre del Pokémon</label>
          <input
            id="query"
            type="text"
            class="form-control"
            :class="{ 'is-invalid': showError }"
            placeholder="Ej: pikachu"
            v-model.trim="query"
            @input="onInput"
          />
          <div class="invalid-feedback">
            {{ errorMsg }}
          </div>
        </div>
        <div class="col-auto pt-4">
          <button type="button" class="btn btn-outline-secondary ms-2" @click="limpiar">
            Limpiar
          </button>
        </div>
      </div>
    </form>

    <!-- Estado sin resultados -->
    <div v-if="!loading && !loadError && filtered.length === 0" class="alert alert-warning" role="alert">
      El Pokémon que desea buscar no existe en la lista cargada.
    </div>

    <!-- Lista de resultados -->
    <ul v-else-if="!loading && !loadError" class="list-group">
      <li
        v-for="p in filtered"
        :key="p.id"
        class="list-group-item d-flex align-items-center justify-content-between"
      >
        <span class="fw-semibold text-capitalize">{{ p.nombre }}</span>
        <span class="badge bg-secondary rounded-pill ms-2">ID: {{ p.id }}</span>
        <img :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/official-artwork/${p.id}.png`" alt="p.nombre" class="img-fluid" width="100" height="100"/>
        <img :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/back/shiny/${p.id}.png`" alt="p.nombre" class="img-fluid" width="100" height="100"/>
       

      </li>
    </ul>
  </section>
</template>
