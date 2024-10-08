<template>
  <div class="container">
    <div class="pokemon-search">
      <input
        type="text"
        v-model="searchQuery"
        @input="searchPokemon"
        placeholder="Buscar Pokémon"
      />
      <button @click="toggleFavorites" class="pokemon-favorite">
        {{ showFavorites ? 'Mostrar Todos' : 'Mostrar Favoritos' }}
      </button>

      <img src="../assets/pokemon.png" alt="Logo Pokemon" class="pokemon-img" />
    </div>

    <section v-if="filteredPokemons.length > 0" class="pokemon-section">
      <PokemonCard
        v-for="pokemon in filteredPokemons"
        :key="pokemon.name"
        :pokemonName="pokemon.name"
        :pokemonImage="pokemon.sprites.other['official-artwork'].front_default"
        :pokemonId="pokemon.id"
        :pokemonType="pokemon.types[0].type.name"
        @dblclick="selectPokemon(pokemon)"
        class="pokemon-card"
      />
    </section>

    <button @click="goToFirstPage" class="back-to-start-btn">Voltar ao Início</button>

    <Pagination :page="page" @next="nextPage" @previous="previousPage" />

    <Modal
      :isVisible="selectedPokemon !== null"
      @close="selectedPokemon = null"
      class="pokemon-modal"
    >
      <h2>{{ selectedPokemon?.name }}</h2>
      <div>
        <p>HP: {{ selectedPokemon?.stats[0].base_stat }}</p>
        <p>Attack: {{ selectedPokemon?.stats[1].base_stat }}</p>
        <p>Defense: {{ selectedPokemon?.stats[2].base_stat }}</p>
        <p>Special Attack: {{ selectedPokemon?.stats[3].base_stat }}</p>
        <p>Special Defense: {{ selectedPokemon?.stats[4].base_stat }}</p>
        <p>Speed: {{ selectedPokemon?.stats[5].base_stat }}</p>
      </div>
      <div>
        <h3>Tipo(s):</h3>
        <div v-for="type in selectedPokemon?.types" :key="type.type.name">
          <p>{{ type.type.name }}</p>
        </div>
      </div>
    </Modal>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import Pagination from '../components/PaginationButtons.vue'
import PokemonCard from '../components/PokemonCard.vue'
import Modal from '../components/Modal/Modal.vue'
import axios from 'axios'

interface Pokemon {
  name: string
  id: number
  sprites: {
    other: {
      'official-artwork': {
        front_default: string
      }
    }
  }
  types: { type: { name: string } }[]
  stats: { base_stat: number }[]
}

export default defineComponent({
  components: { Pagination, PokemonCard, Modal },

  data() {
    return {
      pokemons: [] as Pokemon[],
      filteredPokemons: [] as Pokemon[],
      searchQuery: '',
      page: 1,
      pokemonsPerPage: 6,
      selectedPokemon: null as Pokemon | null,
      showFavorites: false,
      favoritePokemons: [] as number[]
    }
  },
  methods: {
    async fetchPokemons() {
      const responses = await Promise.all(
        Array.from({ length: 1000 }, (_, i) => this.getPokemon(i + 1))
      )
      this.pokemons = responses.map((res) => res.data)
      this.filteredPokemons = this.pokemons.slice(0, this.pokemonsPerPage)
      this.updateFilteredPokemons()
    },
    async getPokemon(pokemonId: Number) {
      return await axios.get(`https://pokeapi.co/api/v2/pokemon/${pokemonId}`)
    },
    async searchPokemon() {
      if (this.searchQuery) {
        try {
          const result = await axios.get(
            `https://pokeapi.co/api/v2/pokemon/${this.searchQuery.toLowerCase()}`
          )
          this.filteredPokemons = [result.data]
        } catch (error) {
          console.error('Pokémon não encontrado', error)
          this.filteredPokemons = []
        }
      } else {
        this.getPagePokemons()
        this.updateFilteredPokemons()
      }
    },

    async getPagePokemons() {
      const startIndex = (this.page - 1) * this.pokemonsPerPage
      this.filteredPokemons = this.pokemons.slice(startIndex, startIndex + this.pokemonsPerPage)
    },

    toggleFavorites() {
      this.showFavorites = !this.showFavorites
      this.updateFilteredPokemons()
    },

    nextPage() {
      this.page++
      this.getPagePokemons()
    },

    previousPage() {
      if (this.page > 1) {
        this.page--
        this.getPagePokemons()
      }
    },

    updateFilteredPokemons() {
      if (this.showFavorites) {
        const favorites = JSON.parse(localStorage.getItem('favoritePokemons') || '[]')
        this.filteredPokemons = this.pokemons.filter((pokemon) => favorites.includes(pokemon.id))
      } else {
        this.getPagePokemons()
      }
    },

    selectPokemon(pokemon: Pokemon) {
      this.selectedPokemon = pokemon
    },

    goToFirstPage() {
      this.page = 1
      this.getPagePokemons()
    }
  },

  mounted() {
    this.fetchPokemons()
    this.favoritePokemons = JSON.parse(localStorage.getItem('favoritePokemons') || '[]')
  }
})
</script>

<style lang="scss" scoped>
.container {
  max-width: 100vw;
  overflow-x: hidden;
  overflow-y: auto;
  padding: 0 1rem;
}
.pokemon-search {
  display: flex;
  justify-content: space-between;
}

.pokemon-search input {
  width: 18rem;
  height: 2.4rem;
  padding: 0.625rem 1.25rem;
  margin: 0.938rem 0 0.625rem 1.875rem;
  border: 0.125rem solid #181716;
  border-radius: 3.125rem;
  font-size: 1rem;
  color: #333;
  background-color: #f9f9f9;
  outline: none;
  transition: all 0.3s ease;
  box-shadow: 0 0.25rem 0.375rem rgba(0, 0, 0, 0.1);
}

.pokemon-search input::placeholder {
  color: #aaa;
  font-style: italic;
  text-align: center;
}

.pokemon-img {
  width: 15rem;
  height: 5rem;
  margin-top: -0.1rem;
  margin-right: 5.5rem;
}

.pokemon-favorite {
  cursor: pointer;
  display: flex;
  align-items: center;
  margin: 2rem auto;
  padding: 0.5rem 1rem;
  background-color: #ffcc01;
  border: none;
  border-radius: 0.5rem;
  cursor: pointer;
  font-size: 1rem;
  color: #333;
  transition: background-color 0.3s ease;
}
.pokemon-favorite:hover {
  background-color: #ffaa00;
}

.pokemon-section {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  width: 100%;
  gap: 1rem;
  margin: 3rem 0;
  padding: 0 2rem;
  opacity: 1;
}

.pokemon-modal {
  h2 {
    text-transform: uppercase;
  }
  p {
    font-size: 1rem;
  }
  p::first-letter {
    text-transform: uppercase;
  }
}

.pokemon-card {
  cursor: pointer;
  width: 100%;
  max-width: 30rem;
  height: 18rem;
  margin-top: 1.456rem;
  transition: all 0.3s;
  opacity: 0.8;
}

.pokemon-card:hover {
  transform: translateY(-1.25rem);
  opacity: 1;
}
.back-to-start-btn {
  display: block;
  margin: 2rem auto;
  padding: 0.5rem 1rem;
  background-color: #ffcc01;
  border: none;
  border-radius: 0.5rem;
  cursor: pointer;
  font-size: 1rem;
  color: #333;
  transition: background-color 0.3s ease;
}

.back-to-start-btn:hover {
  background-color: #ffaa00;
}

@media (max-width: 768px) {
  .container {
    height: 100vh;
    max-width: 100vw;
    align-items: center;
    justify-content: center;
    overflow-x: hidden;
    overflow-y: auto;
  }

  .pokemon-search {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-wrap: wrap;
    width: 100%;
    padding: 1rem;
  }

  .pokemon-search input {
    width: 90%;
    margin: 0;
  }

  .pokemon-img {
    display: none;
  }

  .pokemon-section {
    flex-direction: column;
    overflow-y: scroll;
  }

  .pokemon-card {
    width: 98%;
    height: 18rem;
    margin-top: 1.456rem;
    opacity: none;
  }

  .pokemon-card:hover {
    transform: scale(1);
  }

  .pokemon-favorite {
    overflow-x: hidden;
    overflow-y: auto;
    width: 42%;
  }
}
</style>
