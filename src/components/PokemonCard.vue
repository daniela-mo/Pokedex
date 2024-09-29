<template>
  <div class="pokemon-card-container" :style="{ background: background }" @click="$emit('click')">
    <div class="pokemon-favorite-icon" @click="toggleFavorite">
      <i :class="isFavorite ? 'fas fa-heart' : 'far fa-heart'"></i>
    </div>
    <h3 class="pokemon-name">#{{ pokemonId }} - {{ pokemonName }}</h3>

    <img :src="pokemonImage" alt="pokemon" class="pokemon-image" />

    <p class="pokemon-type">{{ pokemonType }}</p>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  props: {
    pokemonName: {
      type: String,
      required: true
    },
    pokemonImage: {
      type: String
    },
    pokemonType: {
      type: String
    },
    pokemonId: {
      type: Number
    }
  },
  data() {
    return {
      isFavorite: false
    }
  },
  setup() {
    return {}
  },
  computed: {
    background() {
      if (this.pokemonType === 'grass') {
        return '#00FFAB'
      } else if (this.pokemonType === 'bug') {
        return '#2d5639'
      } else if (this.pokemonType === 'fire') {
        return '#f7768a'
      } else if (this.pokemonType === 'water') {
        return '#12c3f7'
      } else if (this.pokemonType === 'flying') {
        return '#92b1c6'
      } else if (this.pokemonType === 'poison') {
        return '#7e5cd6'
      } else if (this.pokemonType === 'doison') {
        return '#5e3382'
      } else if (this.pokemonType === 'normal') {
        return '#cc9aa9'
      } else if (this.pokemonType === 'electric') {
        return '#e2e129'
      } else if (this.pokemonType === 'ground') {
        return '#ab6d29'
      } else if (this.pokemonType === 'fairy') {
        return '#e81266'
      } else if (this.pokemonType === 'fighting') {
        return '#ec5f35'
      } else if (this.pokemonType === 'psychic') {
        return '#f71c92'
      } else if (this.pokemonType === 'rock') {
        return '#8a3d21'
      } else if (this.pokemonType === 'ice') {
        return '#8acfed'
      } else if (this.pokemonType === 'dragon') {
        return '#438893'
      } else if (this.pokemonType === 'dark') {
        return '#585978'
      } else if (this.pokemonType === 'ghost') {
        return '#8f6690'
      } else if (this.pokemonType === 'steel') {
        return '#647670'
      }
      return '#fff'
    }
  },
  mounted() {
    const favorites = JSON.parse(localStorage.getItem('favoritePokemons') || '[]')
    this.isFavorite = favorites.includes(this.pokemonId)
  },
  methods: {
    toggleFavorite() {
      let favorites = JSON.parse(localStorage.getItem('favoritePokemons') || '[]')

      if (this.isFavorite) {
        favorites = favorites.filter((id: number) => id !== this.pokemonId)
      } else {
        favorites.push(this.pokemonId)
      }

      localStorage.setItem('favoritePokemons', JSON.stringify(favorites))
      this.isFavorite = !this.isFavorite
    }
  }
})
</script>

<style lang="scss" scoped>
.pokemon-card-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 18.5rem;
  width: 30rem;
  border-radius: 1rem;

  .pokemon-name {
    padding: 0.25rem 0.75rem;
    border-radius: 1rem;
    background: #00000040;
    color: #fff;
    font-size: 1.125rem;
    text-transform: uppercase;
  }

  .pokemon-favorite-icon {
    width: 100%;
    display: flex;
    justify-content: flex-end;
    margin-right: 1rem;
  }
  .pokemon-favorite-icon i {
    color: #0e0d0d;
  }
  .pokemon-favorite-icon i.fas {
    color: #c30000;
  }
  .pokemon-type {
    padding: 0.25rem 0.75rem;
    border-radius: 1rem;
    background: #00000040;
    color: #fff;
    font-size: 1rem;
  }
  p::first-letter {
    text-transform: uppercase;
  }
  .pokemon-image {
    max-width: 6.25rem;
    margin: 3rem 0;
  }
}
</style>
