<template>
  <div class="main">
    <h1>
      POKÉDEX
    </h1>
    <Pokedex/>

  </div>
</template>

<script>
import Pokedex from './components/Pokedex.vue';

export default {
  components: {
    Pokedex
  }
}
</script>

<style scoped>
.main {
  @apply min-w-full min-h-full mx-auto;
  @apply flex flex-col gap-4 items-center align-middle;
}

h1 {
  @apply text-black font-bold text-2xl;
}
</style>
