<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'
import Autocomplete from './components/Autocomplete.vue';
import PlayerTable from './components/PlayerTable.vue';
import { ref, computed } from 'vue';
import playerList from './data/players.json'

const players = ref(playerList)

const search = ref('')

const filteredPlayers = computed( () => {
  return search.value.length > 0 ?
    players.value.filter( player => searchIndex(player.name) != -1 ) :
    []
})

const searchIndex = (player: string) => player.toLowerCase().search(search.value.toLowerCase())
</script>

<template>
  <div class="container">
    <Autocomplete :options="players.map( player => player.name )" @search="searchText => search = searchText" />
    <PlayerTable :players="filteredPlayers" />
  </div>
</template>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 3rem;;
}
</style>
