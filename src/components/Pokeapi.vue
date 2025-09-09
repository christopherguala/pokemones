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
        <div class="card pokemon-card h-100" :style="{ backgroundColor: getColor(pokemon.tipos[0]) }">
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

            
            <div class="d-flex gap-2 mt-2">
              <button
                class="btn btn-danger btn-sm"
                @click="addToTeam(pokemon, 'A')"
              >
                Equipo A
              </button>
              <button
                class="btn btn-primary btn-sm"
                @click="addToTeam(pokemon, 'B')"
              >
                Equipo B
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>

    <hr />

    <!-- Equipos -->
    <div class="row mt-4">
      <div class="col-md-6">
        <h4>Equipo A ({{ teamA.length }}/6)</h4>
        <ul class="list-group">
          <li v-for="p in teamA" :key="p.id" class="list-group-item">
            <img :src="p.sprite" width="30" class="me-2" /> {{ p.nombre }}
          </li>
        </ul>
      </div>

      <div class="col-md-6">
        <h4>Equipo B ({{ teamB.length }}/6)</h4>
        <ul class="list-group">
          <li v-for="p in teamB" :key="p.id" class="list-group-item">
            <img :src="p.sprite" width="30" class="me-2" /> {{ p.nombre }}
          </li>
        </ul>
      </div>
    </div>

   
    <div
      class="modal fade"
      id="pokemonModal"
      tabindex="-1"
      aria-hidden="true"
      ref="modalRef"
    >
      <div class="modal-dialog modal-dialog-centered">
        <div class="modal-content text-center">
          <div class="modal-header">
            <h5 class="modal-title">¡Pokémon agregado!</h5>
            <button type="button" class="btn-close" @click="closeModal"></button>
          </div>
          <div class="modal-body">
            <img :src="modalPokemon.sprite" width="100" class="mb-2" />
            <p class="text-capitalize">{{ modalPokemon.nombre }}</p>
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
      teamA: [],
      teamB: [],
      modalPokemon: {},
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

    addToTeam(pokemon, team) {
      if (team === "A" && this.teamA.length < 6) {
        this.teamA.push(pokemon);
        this.showModal(pokemon);
      } else if (team === "B" && this.teamB.length < 6) {
        this.teamB.push(pokemon);
        this.showModal(pokemon);
      } else {
        alert("El equipo ya tiene 6 Pokémon.");
      }
    },

  
    showModal(pokemon) {
      this.modalPokemon = pokemon;
      const modal = new bootstrap.Modal(this.$refs.modalRef);
      modal.show();
    },

    closeModal() {
      const modal = bootstrap.Modal.getInstance(this.$refs.modalRef);
      modal.hide();
    },

    getColor(tipo) {
      const colores = {
        fire: "#F08030",
        water: "#6890F0",
        grass: "#78C850",
        electric: "#F8D030",
        psychic: "#F85888",
        ice: "#98D8D8",
        dragon: "#7038F8",
        dark: "#705848",
        fairy: "#EE99AC",
        normal: "#A8A878",
        ground: "#E0C068",
        flying: "#A890F0",
        bug: "#A8B820",
        rock: "#B8A038",
        ghost: "#705898",
        steel: "#B8B8D0",
        poison: "#A040A0",
        fighting: "#C03028",
      };
      return colores[tipo] || "#3b4cca";
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
