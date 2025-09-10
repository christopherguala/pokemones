<template>
  <div class="battle-container">
    <!-- Pantalla de Ganador -->
    <div v-if="showWinner && !isTie" class="winner-screen">
      <div class="winner-banner">
        <h1 class="winner-text">¡FELICIDADES! GANÓ EL EQUIPO {{ winnerTeam }}</h1>
      </div>
      <div class="winner-pokemon">
        <div class="winner-team-display">
          <div 
            v-for="pokemon in winningTeam" 
            :key="pokemon.id" 
            class="winner-pokemon-card"
          >
            <img :src="pokemon.sprite" :alt="pokemon.nombre" />
            <h3 class="text-capitalize">{{ pokemon.nombre }}</h3>
            <p>Puntos: {{ pokemon.battlePoints }}</p>
          </div>
        </div>
        <button class="btn btn-success btn-lg mt-4" @click="resetGame">
          Nueva Batalla
        </button>
      </div>
    </div>

    <!-- Pantalla de Empate -->
    <div v-if="showWinner && isTie" class="tie-screen">
      <div class="tie-banner">
        <h1 class="tie-text">¡FUE UN EMPATE!</h1>
        <p class="tie-subtitle">Ambos equipos tienen {{ teamAPoints }} puntos</p>
      </div>
      <div class="tie-teams">
        <!-- Equipo A -->
        <div class="tie-team">
          <h2 class="tie-team-title">EQUIPO A</h2>
          <div class="tie-team-display">
            <div 
              v-for="pokemon in teamA" 
              :key="pokemon.id" 
              class="tie-pokemon-card"
            >
              <img :src="pokemon.sprite" :alt="pokemon.nombre" />
              <h4 class="text-capitalize">{{ pokemon.nombre }}</h4>
              <p>Puntos: {{ pokemon.battlePoints }}</p>
            </div>
          </div>
        </div>

        <!-- VS -->
        <div class="vs-container">
          <div class="vs-text">VS</div>
        </div>

        <!-- Equipo B -->
        <div class="tie-team">
          <h2 class="tie-team-title">EQUIPO B</h2>
          <div class="tie-team-display">
            <div 
              v-for="pokemon in teamB" 
              :key="pokemon.id" 
              class="tie-pokemon-card"
            >
              <img :src="pokemon.sprite" :alt="pokemon.nombre" />
              <h4 class="text-capitalize">{{ pokemon.nombre }}</h4>
              <p>Puntos: {{ pokemon.battlePoints }}</p>
            </div>
          </div>
        </div>
      </div>
      <button class="btn btn-warning btn-lg mt-4" @click="resetGame">
        Nueva Batalla
      </button>
    </div>

    <!-- Pantalla Principal de Batalla -->
    <div v-else class="battle-main">
      <!-- Layout Principal -->
      <div class="main-layout">
        <!-- Sidebar Equipo A (Izquierda) -->
        <aside class="team-sidebar team-a">
          <h3 class="team-title">EQUIPO A ({{ teamA.length }}/6)</h3>
          <div class="team-pokemon">
            <div 
              v-for="(pokemon, index) in teamA" 
              :key="`${pokemon.id}-${index}`" 
              class="team-pokemon-card"
            >
              <img :src="pokemon.sprite" :alt="pokemon.nombre" />
              <div class="pokemon-info">
                <h6 class="text-capitalize">{{ pokemon.nombre }}</h6>
                <small>Puntos: {{ pokemon.battlePoints }}</small>
              </div>
              <button 
                class="btn btn-outline-danger btn-sm remove-btn"
                @click="removeFromTeam('A', index)"
              >
                ✕
              </button>
            </div>
          </div>
        </aside>

        <!-- Área Principal -->
        <main class="main-content">
          <!-- Buscador de Pokémon -->
          <div class="search-section">
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
          </div>

          <!-- Botón de Pelea 3D -->
          <div class="battle-button-container" v-if="canBattle">
            <button class="battle-button-3d" @click="startBattle">
              <span class="battle-text">PELEA</span>
              <div class="battle-glow"></div>
            </button>
          </div>

          <!-- Grid de Pokémon -->
          <div class="pokemon-grid">
            <div
              class="pokemon-card"
              v-for="pokemon in pokemonesFiltrados"
              :key="pokemon.id"
            >
              <div class="card" :style="{ backgroundColor: getColor(pokemon.tipos[0]) }">
                <img
                  class="card-img-top bg-white p-3"
                  :src="pokemon.sprite"
                  :alt="pokemon.nombre"
                />
                <div class="card-body text-white">
                  <h5 class="card-title text-capitalize">{{ pokemon.nombre }}</h5>
                  <p><strong>Pokedex:</strong> {{ pokemon.id }}</p>
                  <p><strong>Tipo(s):</strong> {{ pokemon.tipos.join(', ') }}</p>
                  <p><strong>Puntos de Batalla:</strong> {{ pokemon.battlePoints }}</p>
                  
                  <div class="d-flex gap-2 mt-2">
                    <button
                      class="btn btn-danger btn-sm"
                      @click="addToTeam(pokemon, 'A')"
                      :disabled="teamA.length >= 6 || isPokemonInTeam(pokemon, 'A')"
                      :class="{ 'btn-outline-danger': isPokemonInTeam(pokemon, 'A') }"
                    >
                      {{ isPokemonInTeam(pokemon, 'A') ? 'Ya en A' : 'Equipo A' }}
                    </button>
                    <button
                      class="btn btn-primary btn-sm"
                      @click="addToTeam(pokemon, 'B')"
                      :disabled="teamB.length >= 6 || isPokemonInTeam(pokemon, 'B')"
                      :class="{ 'btn-outline-primary': isPokemonInTeam(pokemon, 'B') }"
                    >
                      {{ isPokemonInTeam(pokemon, 'B') ? 'Ya en B' : 'Equipo B' }}
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </main>

        <!-- Sidebar Equipo B (Derecha) -->
        <aside class="team-sidebar team-b">
          <h3 class="team-title">EQUIPO B ({{ teamB.length }}/6)</h3>
          <div class="team-pokemon">
            <div 
              v-for="(pokemon, index) in teamB" 
              :key="`${pokemon.id}-${index}`" 
              class="team-pokemon-card"
            >
              <img :src="pokemon.sprite" :alt="pokemon.nombre" />
              <div class="pokemon-info">
                <h6 class="text-capitalize">{{ pokemon.nombre }}</h6>
                <small>Puntos: {{ pokemon.battlePoints }}</small>
              </div>
              <button 
                class="btn btn-outline-danger btn-sm remove-btn"
                @click="removeFromTeam('B', index)"
              >
                ✕
              </button>
            </div>
          </div>
        </aside>
      </div>
    </div>

    <!-- Notificación de Pokémon Agregado -->
    <div v-if="showNotification" class="notification-overlay">
      <div class="notification-card" :class="notificationType">
        <div class="notification-content">
          <img :src="notificationPokemon.sprite" width="60" class="mb-2" />
          <h5 class="text-capitalize">{{ notificationPokemon.nombre }}</h5>
          <p class="notification-text">{{ notificationMessage }}</p>
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
      showWinner: false,
      winnerTeam: '',
      winningTeam: [],
      isTie: false,
      teamAPoints: 0,
      teamBPoints: 0,
      showNotification: false,
      notificationPokemon: {},
      notificationMessage: '',
      notificationType: 'success', // 'success' o 'warning'
      notificationTimeout: null,
    };
  },
  computed: {
    pokemonesFiltrados() {
      if (!this.searchQuery.trim()) return this.pokemones;
      const query = this.searchQuery.toLowerCase();
      return this.pokemones.filter((p) => p.nombre.includes(query));
    },
    canBattle() {
      return this.teamA.length === 6 && this.teamB.length === 6;
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
            battlePoints: this.calculateBattlePoints(data),
          };
        });

        this.pokemones = await Promise.all(fetches);
      } catch (error) {
        this.formError = "Error al cargar los Pokémon.";
        console.error(error);
      }
    },

    calculateBattlePoints(pokemonData) {
      // Calcular puntos de batalla basados en estadísticas base
      const baseStats = pokemonData.stats.reduce((total, stat) => {
        return total + stat.base_stat;
      }, 0);
      
      // Agregar bonus por tipo (algunos tipos son más fuertes)
      const typeBonus = pokemonData.types.length * 10;
      
      // Agregar bonus por rareza (Pokémon con ID más alto son más raros)
      const rarityBonus = Math.floor(pokemonData.id / 10);
      
      return baseStats + typeBonus + rarityBonus;
    },

    limpiarBusqueda() {
      this.searchQuery = "";
      this.formError = null;
    },

    addToTeam(pokemon, team) {
      // Verificar si el equipo está lleno
      if (team === "A" && this.teamA.length >= 6) {
        alert("El equipo A ya tiene 6 Pokémon.");
        return;
      }
      
      if (team === "B" && this.teamB.length >= 6) {
        alert("El equipo B ya tiene 6 Pokémon.");
        return;
      }

      // Verificar si el Pokémon ya está en el equipo
      if (team === "A") {
        const alreadyInTeam = this.teamA.some(p => p.id === pokemon.id);
        if (alreadyInTeam) {
          this.showWarningMessage(pokemon, 'A');
          return;
        }
        this.teamA.push(pokemon);
        this.showNotificationMessage(pokemon, 'A');
      } else if (team === "B") {
        const alreadyInTeam = this.teamB.some(p => p.id === pokemon.id);
        if (alreadyInTeam) {
          this.showWarningMessage(pokemon, 'B');
          return;
        }
        this.teamB.push(pokemon);
        this.showNotificationMessage(pokemon, 'B');
      }
    },

    showNotificationMessage(pokemon, team) {
      // Limpiar timeout anterior si existe
      if (this.notificationTimeout) {
        clearTimeout(this.notificationTimeout);
      }

      this.notificationPokemon = pokemon;
      this.notificationMessage = `Pokémon ${pokemon.nombre} agregado al equipo ${team}`;
      this.notificationType = 'success';
      this.showNotification = true;

      // Cerrar automáticamente después de 2 segundos
      this.notificationTimeout = setTimeout(() => {
        this.showNotification = false;
      }, 2000);
    },

    showWarningMessage(pokemon, team) {
      // Limpiar timeout anterior si existe
      if (this.notificationTimeout) {
        clearTimeout(this.notificationTimeout);
      }

      this.notificationPokemon = pokemon;
      this.notificationMessage = `Este Pokémon ya está en tu equipo ${team}`;
      this.notificationType = 'warning';
      this.showNotification = true;

      // Cerrar automáticamente después de 3 segundos (más tiempo para leer la advertencia)
      this.notificationTimeout = setTimeout(() => {
        this.showNotification = false;
      }, 3000);
    },

    removeFromTeam(team, index) {
      if (team === "A") {
        this.teamA.splice(index, 1);
      } else if (team === "B") {
        this.teamB.splice(index, 1);
      }
    },

    isPokemonInTeam(pokemon, team) {
      if (team === "A") {
        return this.teamA.some(p => p.id === pokemon.id);
      } else if (team === "B") {
        return this.teamB.some(p => p.id === pokemon.id);
      }
      return false;
    },

    startBattle() {
      if (!this.canBattle) return;
      
      // Calcular puntos totales de cada equipo
      this.teamAPoints = this.teamA.reduce((total, pokemon) => total + pokemon.battlePoints, 0);
      this.teamBPoints = this.teamB.reduce((total, pokemon) => total + pokemon.battlePoints, 0);
      
      // Determinar resultado
      if (this.teamAPoints > this.teamBPoints) {
        // Equipo A gana
        this.winnerTeam = 'A';
        this.winningTeam = [...this.teamA];
        this.isTie = false;
      } else if (this.teamBPoints > this.teamAPoints) {
        // Equipo B gana
        this.winnerTeam = 'B';
        this.winningTeam = [...this.teamB];
        this.isTie = false;
      } else {
        // Empate
        this.isTie = true;
        this.winnerTeam = '';
        this.winningTeam = [];
      }
      
      this.showWinner = true;
    },

    resetGame() {
      this.teamA = [];
      this.teamB = [];
      this.showWinner = false;
      this.winnerTeam = '';
      this.winningTeam = [];
      this.isTie = false;
      this.teamAPoints = 0;
      this.teamBPoints = 0;
      this.showNotification = false;
      if (this.notificationTimeout) {
        clearTimeout(this.notificationTimeout);
      }
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
/* Estilos generales */
body {
  font-family: 'Poppins', sans-serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  min-height: 100vh;
}

.battle-container {
  min-height: 100vh;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  padding: 20px;
}

/* Pantalla de Ganador */
.winner-screen {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  text-align: center;
  background: linear-gradient(45deg, #ff6b6b, #4ecdc4, #45b7d1, #96ceb4);
  background-size: 400% 400%;
  animation: gradientShift 3s ease infinite;
}

/* Pantalla de Empate */
.tie-screen {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  text-align: center;
  background: linear-gradient(45deg, #f39c12, #e67e22, #d35400, #e74c3c);
  background-size: 400% 400%;
  animation: gradientShift 3s ease infinite;
  padding: 20px;
}

@keyframes gradientShift {
  0% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
  100% { background-position: 0% 50%; }
}

.winner-banner {
  background: linear-gradient(45deg, #ffd700, #ffed4e);
  padding: 30px 60px;
  border-radius: 20px;
  box-shadow: 0 20px 40px rgba(0,0,0,0.3);
  margin-bottom: 40px;
  transform: rotate(-2deg);
  animation: bounce 2s infinite;
}

@keyframes bounce {
  0%, 20%, 50%, 80%, 100% { transform: translateY(0) rotate(-2deg); }
  40% { transform: translateY(-10px) rotate(-2deg); }
  60% { transform: translateY(-5px) rotate(-2deg); }
}

.winner-text {
  color: #d63031;
  font-size: 3rem;
  font-weight: bold;
  text-shadow: 3px 3px 0px #fff, 6px 6px 0px #d63031;
  margin: 0;
}

/* Estilos para pantalla de empate */
.tie-banner {
  background: linear-gradient(45deg, #f1c40f, #f39c12);
  padding: 30px 60px;
  border-radius: 20px;
  box-shadow: 0 20px 40px rgba(0,0,0,0.3);
  margin-bottom: 40px;
  transform: rotate(-1deg);
  animation: bounce 2s infinite;
}

.tie-text {
  color: #8e44ad;
  font-size: 3rem;
  font-weight: bold;
  text-shadow: 3px 3px 0px #fff, 6px 6px 0px #8e44ad;
  margin: 0;
}

.tie-subtitle {
  color: #2c3e50;
  font-size: 1.2rem;
  font-weight: bold;
  margin: 10px 0 0 0;
  text-shadow: 1px 1px 2px rgba(255,255,255,0.8);
}

.tie-teams {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 40px;
  margin-bottom: 30px;
  flex-wrap: wrap;
}

.tie-team {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border-radius: 20px;
  padding: 25px;
  border: 2px solid rgba(255, 255, 255, 0.2);
  min-width: 300px;
}

.tie-team-title {
  color: white;
  font-size: 1.5rem;
  font-weight: bold;
  text-shadow: 2px 2px 4px rgba(0,0,0,0.5);
  margin-bottom: 20px;
  padding: 10px;
  background: rgba(0,0,0,0.2);
  border-radius: 10px;
}

.tie-team-display {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 15px;
}

.tie-pokemon-card {
  background: white;
  padding: 15px;
  border-radius: 12px;
  box-shadow: 0 8px 25px rgba(0,0,0,0.2);
  transform: scale(1);
  transition: transform 0.3s ease;
}

.tie-pokemon-card:hover {
  transform: scale(1.05);
}

.tie-pokemon-card img {
  width: 60px;
  height: 60px;
  object-fit: contain;
}

.tie-pokemon-card h4 {
  margin: 8px 0 5px 0;
  color: #2c3e50;
  font-size: 0.9rem;
}

.tie-pokemon-card p {
  margin: 0;
  color: #7f8c8d;
  font-size: 0.8rem;
}

.vs-container {
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(255, 255, 255, 0.2);
  border-radius: 50%;
  width: 80px;
  height: 80px;
  backdrop-filter: blur(10px);
  border: 3px solid rgba(255, 255, 255, 0.3);
}

.vs-text {
  color: white;
  font-size: 2rem;
  font-weight: bold;
  text-shadow: 2px 2px 4px rgba(0,0,0,0.5);
  animation: pulse 2s infinite;
}

.winner-team-display {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
  margin-bottom: 30px;
}

.winner-pokemon-card {
  background: white;
  padding: 20px;
  border-radius: 15px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.2);
  transform: scale(1);
  transition: transform 0.3s ease;
}

.winner-pokemon-card:hover {
  transform: scale(1.1);
}

.winner-pokemon-card img {
  width: 80px;
  height: 80px;
  object-fit: contain;
}

/* Sección de búsqueda */
.search-section {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  padding: 20px;
  border-radius: 15px;
  margin-bottom: 30px;
  border: 1px solid rgba(255, 255, 255, 0.2);
}

/* Grid de Pokémon */
.pokemon-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 20px;
  margin-bottom: 40px;
}

.pokemon-card .card {
  border: none;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.2);
  border-radius: 15px;
  overflow: hidden;
  transition: all 0.3s ease;
  background: linear-gradient(145deg, #f0f0f0, #cacaca);
}

.pokemon-card .card:hover {
  transform: translateY(-10px) scale(1.02);
  box-shadow: 0 15px 35px rgba(0, 0, 0, 0.3);
}

.pokemon-card .card-body {
  border-radius: 0 0 15px 15px;
  color: white;
  text-shadow: 1px 1px 2px rgba(0,0,0,0.5);
}

/* Layout Principal */
.main-layout {
  display: grid;
  grid-template-columns: 250px 1fr 250px;
  gap: 20px;
  min-height: calc(100vh - 40px);
}

/* Sidebars de Equipos */
.team-sidebar {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border-radius: 15px;
  padding: 20px;
  border: 2px solid rgba(255, 255, 255, 0.2);
  height: fit-content;
  position: sticky;
  top: 20px;
}

.team-a {
  border-left: 5px solid #e74c3c;
}

.team-b {
  border-right: 5px solid #3498db;
}

/* Área Principal */
.main-content {
  background: rgba(255, 255, 255, 0.05);
  backdrop-filter: blur(10px);
  border-radius: 15px;
  padding: 20px;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.team-title {
  color: white;
  text-align: center;
  font-size: 1.2rem;
  font-weight: bold;
  text-shadow: 2px 2px 4px rgba(0,0,0,0.5);
  margin-bottom: 15px;
  padding: 8px;
  background: rgba(0,0,0,0.2);
  border-radius: 8px;
}

.team-pokemon {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.team-pokemon-card {
  display: flex;
  align-items: center;
  background: rgba(255, 255, 255, 0.9);
  padding: 10px;
  border-radius: 10px;
  box-shadow: 0 3px 10px rgba(0,0,0,0.1);
  transition: all 0.3s ease;
  position: relative;
}

.team-pokemon-card:hover {
  transform: translateX(3px);
  box-shadow: 0 5px 15px rgba(0,0,0,0.2);
}

.team-pokemon-card img {
  width: 35px;
  height: 35px;
  object-fit: contain;
  margin-right: 10px;
}

.pokemon-info {
  flex: 1;
}

.pokemon-info h5 {
  margin: 0;
  color: #2c3e50;
  font-weight: bold;
}

.pokemon-info p {
  margin: 5px 0 0 0;
  color: #7f8c8d;
  font-size: 0.9rem;
}

.remove-btn {
  position: absolute;
  top: 5px;
  right: 5px;
  width: 25px;
  height: 25px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 12px;
  padding: 0;
}

/* Botón de Pelea 3D */
.battle-button-container {
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  z-index: 10;
}

.battle-button-3d {
  position: relative;
  background: linear-gradient(45deg, #ff6b6b, #ee5a24, #ff6b6b);
  border: none;
  padding: 20px 40px;
  border-radius: 50px;
  font-size: 2rem;
  font-weight: bold;
  color: white;
  text-shadow: 2px 2px 4px rgba(0,0,0,0.5);
  cursor: pointer;
  transform: perspective(1000px) rotateX(15deg);
  transition: all 0.3s ease;
  box-shadow: 
    0 10px 30px rgba(0,0,0,0.3),
    inset 0 1px 0 rgba(255,255,255,0.3),
    inset 0 -1px 0 rgba(0,0,0,0.2);
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0% { transform: perspective(1000px) rotateX(15deg) scale(1); }
  50% { transform: perspective(1000px) rotateX(15deg) scale(1.05); }
  100% { transform: perspective(1000px) rotateX(15deg) scale(1); }
}

.battle-button-3d:hover {
  transform: perspective(1000px) rotateX(10deg) scale(1.1);
  box-shadow: 
    0 15px 40px rgba(0,0,0,0.4),
    inset 0 1px 0 rgba(255,255,255,0.4),
    inset 0 -1px 0 rgba(0,0,0,0.3);
}

.battle-button-3d:active {
  transform: perspective(1000px) rotateX(20deg) scale(0.95);
}

.battle-glow {
  position: absolute;
  top: -5px;
  left: -5px;
  right: -5px;
  bottom: -5px;
  background: linear-gradient(45deg, #ff6b6b, #ee5a24, #ff6b6b);
  border-radius: 55px;
  filter: blur(10px);
  opacity: 0.7;
  z-index: -1;
  animation: glow 2s ease-in-out infinite alternate;
}

@keyframes glow {
  from { opacity: 0.7; }
  to { opacity: 1; }
}

.battle-text {
  position: relative;
  z-index: 2;
}

/* Notificaciones */
.notification-overlay {
  position: fixed;
  top: 20px;
  right: 20px;
  z-index: 1000;
  animation: slideIn 0.3s ease-out;
}

@keyframes slideIn {
  from {
    transform: translateX(100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

.notification-card {
  border-radius: 15px;
  padding: 20px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.3);
  border: 2px solid rgba(255,255,255,0.2);
  min-width: 250px;
  transition: all 0.3s ease;
}

.notification-card.success {
  background: linear-gradient(135deg, #4ecdc4, #44a08d);
}

.notification-card.warning {
  background: linear-gradient(135deg, #f39c12, #e67e22);
  animation: shake 0.5s ease-in-out;
}

@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-5px); }
  75% { transform: translateX(5px); }
}

.notification-content {
  text-align: center;
  color: white;
}

.notification-content img {
  border-radius: 50%;
  background: rgba(255,255,255,0.2);
  padding: 5px;
}

.notification-content h5 {
  margin: 10px 0 5px 0;
  font-weight: bold;
  text-shadow: 1px 1px 2px rgba(0,0,0,0.5);
}

.notification-text {
  margin: 0;
  font-size: 0.9rem;
  opacity: 0.9;
}

/* Responsive */
@media (max-width: 1024px) {
  .main-layout {
    grid-template-columns: 200px 1fr 200px;
  }
}

@media (max-width: 768px) {
  .main-layout {
    grid-template-columns: 1fr;
    gap: 15px;
  }
  
  .team-sidebar {
    position: static;
    order: 2;
  }
  
  .main-content {
    order: 1;
  }
  
  .battle-button-container {
    margin: 20px 0;
  }
  
  .winner-team-display {
    grid-template-columns: repeat(2, 1fr);
  }
  
  .winner-text {
    font-size: 2rem;
  }
  
  .tie-text {
    font-size: 2rem;
  }
  
  .tie-teams {
    flex-direction: column;
    gap: 20px;
  }
  
  .vs-container {
    order: -1;
    margin: 20px 0;
  }
  
  .tie-team {
    min-width: auto;
    width: 100%;
  }
  
  .tie-team-display {
    grid-template-columns: repeat(3, 1fr);
  }
  
  .notification-overlay {
    top: 10px;
    right: 10px;
    left: 10px;
  }
  
  .notification-card {
    min-width: auto;
  }
}

@media (max-width: 480px) {
  .winner-team-display {
    grid-template-columns: 1fr;
  }
  
  .winner-text {
    font-size: 1.5rem;
  }
  
  .tie-text {
    font-size: 1.5rem;
  }
  
  .tie-team-display {
    grid-template-columns: repeat(2, 1fr);
  }
  
  .battle-button-3d {
    font-size: 1.5rem;
    padding: 15px 30px;
  }
}
</style>
