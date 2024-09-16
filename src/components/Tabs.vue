<script setup>

    import { ref } from 'vue'; 
    import Home from './Home.vue';
    import AnimeList from './Anime-list.vue';
    let tab = ref(1); 


</script>


<template>

    <!-- Tabs per la navigazione -->
    <div class="w-full my-10">
        <div class="flex justify-center p-5">
            <button 
            @click="tab=1"
            :class="{'btn-secondary' : tab === 1}"
            class="btn btn-xs sm:btn-sm md:btn-md lg:btn-lg">
                HOMEPAGE
            </button>
            <button 
            @click="tab=2"
            :class="{'btn-secondary' : tab === 2}"
            class="btn btn-xs sm:btn-sm md:btn-md lg:btn-lg">
                LISTA ANIME
            </button>

        </div>
        
        <!-- Contenuto --> 
        <div>
            <div v-if="tab === 1"class="py-10 px-6">
                <!-- Contenuto tab1 -->
                <Home />
            </div>
            
            <div v-else class="Tab2 py-10 px-6">
                <!-- Contenuto tab2 -->
                 <AnimeList />
            </div>

        </div>

    </div>

</template>