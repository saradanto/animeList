<script setup>

    import { onMounted , ref, computed } from "vue";
    import axios from "axios";
    

    const showAlert = ref(false); 

    const endpoint = "http://localhost:3000/anime"; 

    let products = ref([]); 

    let watchedAnime = computed(()=> products.value.filter( product => product.completed).length)

    let toWatch = computed(()=> products.value.filter(product => !product.completed).length)

    onMounted(()=>{
        axios.get(endpoint).then((resp)=>{
            products.value = resp.data; 
        })
    })

    function onAddAnime(e) {

        const nameAnime = e.target.value; 
        
        const newAnime = {

            title: nameAnime, 
            completed: false

        }

        axios.post(endpoint, newAnime).then((resp)=> {
            products.value = [...products.value, resp.data]; 

            //reset del campo input

            e.target.value= ""; 
        })
    }

    
    function toggleCompleted(product) {
        
        axios.patch(`${endpoint}/${product.id}`, product ).then((resp)=> {
            showAlert.value = true;
            setTimeout(()=>{
                showAlert.value = false; 
            }, 3000)
        }); 
        
    }

    function deleteAnime(productId) {
        axios.delete(`${endpoint}/${productId}`).then((resp)=>{
           /*  console.log(resp);  */
           products.value = products.value.filter(product => product.id != productId); 
        })
    }



</script>


<template>

    <div class=" w-100 md:w-2/5 mx-auto my-4">
        
        <div v-if="showAlert" role="alert" class=" flex items-center justify-center alert alert-success">
            <svg
                xmlns="http://www.w3.org/2000/svg"
                class="h-6 w-6 shrink-0 stroke-current"
                fill="none"
                viewBox="0 0 24 24">
                <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
            <span>Anime aggiornato con successo</span>
        </div>

    </div>

    <!-- sezione per aggiungere un nuovo prodotto -->
    <div class="w-100 md:w-2/5 mx-auto my-4">

        <label class="input input-bordered input-secondary flex items-center gap-2 ">
        <input @keyup.enter="onAddAnime($event)" type="text" class="grow" placeholder="Inserisci titolo Anime" />
        <span class="badge badge-secondary">Invio</span>
        </label>

    </div>

    <!-- Contatore -->

    <div class="w-full md:w-2/5 mx-auto">

        <div class="flex justify-center justify-evenly">

            <button class="btn btn-xs sm:btn-sm md:btn-md lg:btn-lg">
                Visti
                <div class="badge">{{ watchedAnime }}</div>
            </button>
            <button class="btn btn-xs sm:btn-sm md:btn-md lg:btn-lg">
                Da guardare
                <div class="badge">{{ toWatch }}</div>
            </button>

        </div>

    </div>
    
    

    <!-- sezione lista -->

    <div class="w-100 md:w-2/5 mx-auto my-7">

        <button v-for="product in products" :key="product.id" class="w-full btn btn-xs sm:btn-sm md:btn-md lg:btn-lg flex justify-between my-5 rounded shadow-lg shadow-inner-lg">

            <div class="flex items-center">
                <button @click="deleteAnime(product.id)" class="btn btn-ghost">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6 text-error">
                    <path stroke-linecap="round" stroke-linejoin="round" d="m14.74 9-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 0 1-2.244 2.077H8.084a2.25 2.25 0 0 1-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 0 0-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 0 1 3.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 0 0-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 0 0-7.5 0" />
                    </svg>
                </button>
                <span class="label-text ml-3" :class="{'line-through text-accent': product.completed}">{{product.title}}</span>
            </div>
            <div class="form-control mx-3 my-1">
                <label class="cursor-pointer label">
                <input @change="toggleCompleted(product)" 
                v-model="product.completed"
                type="checkbox" class="checkbox checkbox-secondary" />
                </label>
            </div>
            

        </button>

        <!-- icona - Nome prodotto -->

        <!-- <div  v-for="product in products" :key="product.id" class="flex justify-between">
            
             <div class="flex items-center">
                <button @click="deleteAnime(product.id)" class="btn btn-ghost">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6 text-error">
                    <path stroke-linecap="round" stroke-linejoin="round" d="m14.74 9-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 0 1-2.244 2.077H8.084a2.25 2.25 0 0 1-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 0 0-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 0 1 3.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 0 0-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 0 0-7.5 0" />
                    </svg>
                </button>
                <span class="label-text ml-3" :class="{'line-through text-accent': product.completed}">{{product.title}}</span>
            </div>
            <div class="form-control mx-3 my-1">
                <label class="cursor-pointer label">
                <input @change="toggleCompleted(product)" 
                v-model="product.completed"
                type="checkbox" class="checkbox checkbox-secondary" />
                </label>
            </div>
        </div> -->

    </div>

</template>