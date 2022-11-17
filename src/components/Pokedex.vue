<template>
  <div class="pokedex-container">

    <div class="poke-grid">
      <div v-for='(pokemon, index) in pokemonData' :key='index'>

        <div class="pokemon-base" @click="toggleCard(pokemon)">

          <img v-bind:src="getImage(pokemon)" alt="">

          <div class="overlay">
            <div class="overlay-text">
              <h1>{{getName(pokemon)}}</h1>
              <p>Click for more information!</p>
            </div>
          </div>

        </div>
      </div>
    </div>

    <div class="load-more-button" @click="loadNewPokemons">
      Load more Pokémons!
    </div>

    <div class="card-box" v-show="selectedID > 0">
      <div class="card" v-click-outside="closeCard">
        <Card 
          :pokemon="getPokemonFromID(selectedID)"
          :showing="showCard"/>
      </div>
    </div>

  </div>
</template>

<script>
import axios from 'axios'
import Card from './Card.vue'
import vClickOutside from 'click-outside-vue3'

export default {
  components: {
    Card
  },
  directives: {
      clickOutside: vClickOutside.directive
  },

  data() {
    return {
      maxID: 905,
      amount: 10, // au hasard
      ids: [],
      pokemonData: [],
      selectedID: 0,
      closeCount: 0,
      showCard: false
    }
  },

  mounted() {
    this.loadPokemonIDs();
    console.log("IDs: ", this.ids);
    this.getPokemonData()
    console.log("Pokemon Data:", this.pokemonData);
  },

  methods: {
    // Randomly pick Pokemon IDs (at the start)
    loadPokemonIDs() {
      let i = 0;
      while (i < this.amount) {
        let rand = Math.floor(1 + Math.random() * this.maxID);
        if (!this.ids.includes(rand)){  // Make sure we don't add the same pokemon twice
          this.ids.push(rand);
          i++;
        }
      }
    },
    loadNewPokemons(){
      this.loadPokemonIDs();
      this.getPokemonData();
    },

    // Load data from latest Pokemon IDs
    async getPokemonData() {
      if (this.ids.length === 0){return;}
      for (let i=Math.max(0, this.ids.length - this.amount); i < this.ids.length; i++){
        let pokemon = await axios.get("https://pokeapi.co/api/v2/pokemon/" + this.ids[i]);
        this.pokemonData.push(pokemon.data);
      } 
    },

    toggleCard(pokemon) {
      if (this.selectedID === 0){
        this.selectedID = pokemon.id;
        this.showCard = true;
      }
    },
    closeCard() {
      if (this.selectedID > 0) {
        this.closeCount++;  // Pretty weird, but when opening the card, it counts as "click outside", so only close after 2nd "click outside"
        if (this.closeCount >= 2){
          this.showCard = false;
          setTimeout(() => {
              this.selectedID = 0;
              this.closeCount = 0;
          }, 400)
        }
      }
    },

    getImage(pokemon) {
      return pokemon.sprites.other["official-artwork"].front_default;
    },
    getName(pokemon) {
      return (pokemon.name).toUpperCase();
    },

    getPokemonFromID(id) {
      if (id <= 0) {return null;}
      for (let i=0; i < this.pokemonData.length; i++) {
        if (this.pokemonData[i].id === id){
          console.log("Sending pokemon: " + this.pokemonData[i]);
          return this.pokemonData[i];
        }
      }
      return null;
    }
  }
}
</script>

<style scoped>
.pokedex-container {
  @apply w-full h-full items-center align-middle;
  @apply flex flex-col gap-8;
}
.poke-grid {
  @apply grid grid-cols-5 gap-4 w-full h-full mx-auto items-center align-middle;
}

.pokemon-base {
  @apply relative flex flex-col gap-4 p-4;
  @apply rounded-lg cursor-pointer;
  @apply bg-white;
  box-shadow: 0 0 0.2rem blue;
  overflow: hidden;
  transition: all 0.5s;
}

.pokemon-base:hover {
  box-shadow: 0 0 1rem blue;
}

.overlay {
  @apply absolute w-full h-full top-0 left-0;
  @apply opacity-0;
  background: linear-gradient(to top, transparent 50%, rgb(0, 0, 0, 0.5) 50%);
  background-position: center bottom;
  background-size: 200% 200%;
  transition: all 0.4s;
}
.overlay:hover {
  @apply opacity-100;
  /* @apply bg-black/50; */
  background-position: center top;
}

.overlay-text {
  @apply absolute text-center opacity-100 text-white flex flex-col;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}

h1 {
  @apply font-bold text-lg;
}

button {
  @apply bg-blue-500 text-white p-2;
}
.load-more-button {
  @apply text-black font-normal text-lg cursor-pointer;
  @apply rounded-sm px-4 py-2;
  outline: 1px gray solid;
  background: linear-gradient(0deg, rgba(255,255,255,1) 45%, rgb(255, 106, 106) 55%);
}
.load-more-button:hover {
  @apply font-bold;
  outline: 2px black solid;
}
.card-box {
  @apply z-10 fixed top-0 left-0;
  @apply w-full h-full bg-black/80;
}
.card {
  @apply absolute;
  height: 80%;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

</style>
