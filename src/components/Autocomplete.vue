<script setup lang="ts">
import { reactive, ref, defineProps, computed } from 'vue';
import IconX from './icons/IconX.vue'
import IconSearch from './icons/IconsSearch.vue'

const state = reactive({
    search: ''
})
const showDropdown = ref(false)
const searchAccumulator = ref([])

const props = defineProps<{
    options?: Array<string>
}>()

const emit = defineEmits(['search'])

const autocompleteOptions = computed(() => {
    return [
        ...(
            state.search.length == 0 ?
                searchAccumulator.value :
                (props.options?.filter(option => (searchIndex(option) != -1)) || [])
        )
    ].slice(0, 5) // Selecting the top 5 Results
})

function emitSearch(search: string, event = null) {
    if (event)
        event.preventDefault()

    if (search.length > 0) {
        state.search = search
        searchAccumulator.value.unshift(search)
        showDropdown.value = false
        emit('search', search)
    }
    else {
        showDropdown.value = true
    }
}

const searchIndex = (option: string) => option.toLowerCase().search(state.search.toLowerCase())

const optionHighlight = (option: string) => [
    option.substring(0, searchIndex(option)),
    option.substring(searchIndex(option), searchIndex(option) + state.search.length),
    option.substring(searchIndex(option) + state.search.length)
]

const blur = () => {
    setTimeout(() => {
        showDropdown.value = false
    }, 300)
}
</script>

<template>
    <div>
        <div class="input-container">
            <label class="search-box" for="autocomplete">
                <div class="input-box">
                    <input type="text" class="autocomplete" id="autocomplete" v-model.trim="state.search"
                        @focus="showDropdown = true" @keyup="showDropdown = true" @blur="blur"
                        @keyup.enter="emitSearch(state.search)"
                        placeholder="Search a player">
                    <div class="cancel" @click="state.search = ''">
                        <IconX />
                    </div>
                </div>
                <div class="search" @click="emitSearch(state.search, $event)">
                    <IconSearch />
                </div>
            </label>
            <div v-if="showDropdown && autocompleteOptions?.length > 0" class="dropdown-box">
                <div class="dropdown">
                    <div v-for="(option, index) in autocompleteOptions" :key="index" class="option"
                        @click="emitSearch(option)">
                        {{ optionHighlight(option)[0] }}<span class="match">{{ optionHighlight(option)[1] }}</span>{{
                            optionHighlight(option)[2] }}
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped="" lang="sass">
    .input-container
        .search-box
            display: flex
            border: solid 1px #999
            border-radius: 1rem
            background-color: white
            > *
                padding-right: 0.3rem
                padding-left: 0.3rem
            > * > *
                height: 3rem
            > :not(:last-child)
                border-right: solid 1px #999
            .input-box
                display: flex
                input
                    width: 16rem  
                    border: none
                    box-shadow: none
                    background-color: transparent
                    font-size: 1rem
                input:focus-visible
                    outline: none
            .cancel, .search
                display: flex
                align-items: center
                cursor: pointer
            .search
                padding-left: 0.5rem
                padding-right: 0.5rem
        .search-box:has( input:focus-visible )
            border-color: blue
    .input-container:has( .dropdown .option )
        .search-box
            border-radius: 1rem 1rem 0 0
    .dropdown-box
        height: 0
        z-index: 2
    .dropdown
        border: solid 1px blue
        border-top-width: 0
        border-radius: 0 0 1rem 1rem
        .option
            padding: 0.4rem
            cursor: pointer
            background-color: #efefef
            .match
                font-weight: 700
        .option:not(:last-child)
            border-bottom: solid 1px blue
        .option:last-child
            border-radius: 0 0 1rem 1rem
        .option:hover
            background-color: #ddd

</style>