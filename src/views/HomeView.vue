<template>
  <div class="container">
    <div class="pokemon-search">
      <input
        type="text"
        v-model="searchQuery"
        @input="searchPokemon"
        placeholder="Buscar Pokémon"
      />
      <img src="../assets/pokemon.png" alt="Logo Pokemon" class="pokemon-logo" />
    </div>

    <section v-if="filteredPokemons.length > 0" class="pokemon-section">
      <PokemonCard
        v-for="(pokemon, index) in filteredPokemons"
        :key="pokemon.name"
        :pokemonName="pokemon.name"
        :pokemonImage="pokemon.sprites.other['official-artwork'].front_default"
        :pokemonId="pokemon.id"
        :pokemonType="pokemon.types[0].type.name"
        @click="selectPokemon(pokemon)"
        class="pokemon-card"
      />
    </section>

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
import Pagination from '../components/Pagination.vue'
import PokemonCard from '../components/PokemonCard.vue'
import Modal from '../components/Modal/Modal.vue'
import axios from 'axios'

export default defineComponent({
  components: { Pagination, PokemonCard, Modal },
  data() {
    return {
      pokemons: [],
      filteredPokemons: [],
      searchQuery: '',
      page: 1,
      pokemonsPerPage: 6,
      selectedPokemon: null
    }
  },
  methods: {
    async fetchPokemons() {
      const responses = await Promise.all(
        Array.from({ length: 1000 }, (_, i) => this.getPokemon(i + 1))
      )
      this.pokemons = responses.map((res) => res.data)
      this.filteredPokemons = this.pokemons.slice(0, this.pokemonsPerPage)
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
      }
    },
    async getPagePokemons() {
      const startIndex = (this.page - 1) * this.pokemonsPerPage
      this.filteredPokemons = this.pokemons.slice(startIndex, startIndex + this.pokemonsPerPage)
    },
    selectPokemon(pokemon) {
      this.selectedPokemon = pokemon
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
    }
  },
  mounted() {
    this.fetchPokemons()
  }
})
</script>

<style lang="scss" scoped>
.container {
  max-width: 100vw;
  .pokemon-search {
    display: flex;
    justify-content: space-between;
  }
  .pokemon-search img {
    width: 30rem;
    height: 9.375rem;
    margin-top: -3.125rem;
    margin-right: 5.5rem;
  }
  .pokemon-search input {
    width: 18.75rem;
    height: 2.5rem;
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
  }

  .pokemon-section {
    justify-content: center;
    gap: 1rem;
    width: 100%;
    margin: 3rem 0;
    padding: 0 2rem;
    display: flex;

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

  .pokemon-card:hover {
    transform: translateY(-1.25rem);
    opacity: 1;
  }

  .pokemon-card {
    transition: all 0.3s;
    opacity: 0.8;
  }
}

@media (max-width: 768px) {
  .container {
    max-width: 100vw;
    overflow-x: hidden;
  }

  .pokemon-logo {
    display: none;
  }

  .pokemon-section {
    flex-direction: column;
  }

  .pokemon-card {
    width: 98%;
    height: 18rem;
    margin-top: 1.456rem;
    opacity: none;
  }

  .pokemon-card:hover {
    transform: scale(1);
    transform: none;
  }
}
</style>
