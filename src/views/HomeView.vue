<template>
    <main class="container text-white">
        <div class="pt-4 mb-8 relative">
            <input
                type="text"
                placeholder="Pesquise sua cidade"
                class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-md"
                v-model="searchQuery"
                @input="getCity"
            />
            <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-16"
                v-if="cityResult">
                <p v-if="searchError" class="py-2">Desculpe, algo deu errado, tente novamente.</p>
                <p v-if="!searchError && cityResult.length == 0" class="py-2">Nenhum resultado encontrado, tente outro nome</p>
                <template v-else>
                    <li v-for="searchResult in cityResult"
                        :key="searchResult.id"
                        class="py-2 cursor-pointer"
                        @click="previewCity(searchResult)"
                    >
                    {{searchResult.name}}, {{searchResult.state}}, {{searchResult.country}}
                    </li>
                </template>
            </ul>
        </div>

        <div class="flex flex-col gap-4">
            <Suspense>
                <CityList />
                <template #fallback>
                    <CityCardSkeleton />
                </template>
            </Suspense>
        </div>
    </main>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
import CityList from "../components/CityList.vue"
import CityCardSkeleton from "../components/CityCardSkeleton.vue"

const searchQuery = ref("");
const queryTimeout = ref(null);
const APIkey = "dcd521bfb04e047565cf9bd29657441c"
const cityResult = ref(null)
const searchError = ref(null)
const router = useRouter();


const getCity = () =>{
    clearTimeout(queryTimeout.value)
    queryTimeout.value = setTimeout(async () => {
        if(searchQuery.value != ""){
            try{
                const result = await axios.get(`http://api.openweathermap.org/geo/1.0/direct?q=${searchQuery.value}&appid=${APIkey}&limit=5`)
                cityResult.value = result.data
            }catch{
                searchError.value = true
            }
            return;
        }
        cityResult.value = null
    }, 500);
}
const previewCity = (searchResult)=>{
    router.push({
        name:"cityView",
        params: {state:searchResult.state, city:searchResult.name},
        query:{
            lat:searchResult.lat,
            lon:searchResult.lon,
            preview:true,
        }
    })
}
</script>