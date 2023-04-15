<script setup="" lang="ts">
import { defineProps } from 'vue';

const props = defineProps<{
    players?: Array<{
        name: String,
        team: String
    }>
}>()

</script>

<template>
    <table border="1" cellspacing="0" cellpadding="5" v-if="props.players && props.players.length > 0">
        <thead>
            <tr>
                <th>Player</th>
                <th>Team</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="(player, index) in props.players" :key="index">
                <td>{{ player.name }}</td>
                <td>{{ player.team }}</td>
            </tr>
        </tbody>
    </table>
</template>

<style scoped="" lang="sass">
    table
        border-radius: 1rem
        width: 24rem
        margin-top: 2rem
        thead tr:first-child > th:first-child
            border-top-left-radius: 1rem
        thead tr:first-child > th:last-child
            border-top-right-radius: 1rem
        tbody tr:last-child > td:first-child
            border-bottom-left-radius: 1rem
        tbody tr:last-child > td:last-child
            border-bottom-right-radius: 1rem
        th
            font-weight: 700
</style>