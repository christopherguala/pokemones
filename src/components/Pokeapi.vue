

<template>
  <div class="container mt-4">
    <form @submit.prevent class="mb-3 d-flex gap-2 align-items-start">
      <input
        v-model="searchQuery"
        type="text"
        class="form-control"
        placeholder="Buscar Pokémon por nombre"
      />
      <button class="btn btn-secondary" type="button" @click="limpiarBusqueda">
        Limpiar
      </button>
    </form>

    <div v-if="formError" class="alert alert-danger">
      {{ formError }}
    </div>

    <div class="row">
  <div
    class="col-md-3 mb-4"
    v-for="pokemon in pokemonesFiltrados"
    :key="pokemon.id"
  >
    <div class="card pokemon-card h-100">
      <img
        class="card-img-top bg-white p-3"
        :src="pokemon.sprite"
        :alt="pokemon.nombre"
      />
      <div class="card-body text-white">
        <h5 class="card-title text-capitalize">{{ pokemon.nombre }}</h5>
        <p><strong>Pokedex:</strong> {{ pokemon.id }}</p>
        <p><strong>Tipo(s):</strong> {{ pokemon.tipos.join(', ') }}</p>
        <p><strong>Peso:</strong> {{ pokemon.peso }} kg</p>
        <p><strong>Altura:</strong> {{ pokemon.altura }} M</p>
      </div>
    </div>
  </div>
</div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      searchQuery: "",
      pokemones: [],
      formError: null,
    };
  },
  computed: {
    pokemonesFiltrados() {
      if (!this.searchQuery.trim()) return this.pokemones;
      const query = this.searchQuery.toLowerCase();
      return this.pokemones.filter((p) => p.nombre.includes(query));
    },
  },
  methods: {
    async cargarPokemones() {
      try {
        const limite = 151;
        const ids = Array.from({ length: limite }, (_, i) => i + 1);

        const fetches = ids.map(async (id) => {
          const res = await fetch(`https://pokeapi.co/api/v2/pokemon/${id}`);
          const data = await res.json();
          return {
            id: data.id,
            nombre: data.name,
            sprite: data.sprites.front_default,
            tipos: data.types.map((t) => t.type.name),
            peso: data.weight / 10,
            altura: data.height / 10,
          };
        });

        this.pokemones = await Promise.all(fetches);
      } catch (error) {
        this.formError = "Error al cargar los Pokémon.";
        console.error(error);
      }
    },

    limpiarBusqueda() {
      this.searchQuery = "";
      this.formError = null;
    },
  },
  mounted() {
    this.cargarPokemones();
  },
};
</script>


<style scoped>
body {
  font-family: 'Poppins', sans-serif;
}

.pokemon-card .card-body {
  background: #3b4cca;
  border-radius: 0 0 8px 8px;
  color: white;
}

.pokemon-card {
  border: none;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  border-radius: 8px;
  overflow: hidden;
  transition: transform 0.2s ease;
}

.pokemon-card:hover {
  transform: scale(1.03);
}
</style>