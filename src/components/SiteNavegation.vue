<template>
    <header class="sticky top-0 bg-weather-primary shadow-lg">
        <nav
            class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6"
        >
            <RouterLink :to="{ name: 'home' }">
                <div class="flex items-center gap-3">
                    <i class="fa-solid fa-sun text-2xl"></i>
                    <p class="text-2xl">The Local Weather</p>
                </div>
            </RouterLink>

            <div class="flex gap-3 flex-1 justify-end">
                <i
                    @click="toggleModal"
                    class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer"
                ></i>
                <i
                    class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer"
                    @click="addCity"
                    v-if="route.query.preview"
                ></i>
            </div>
            <BaseModal :modalActive="modalActive" @close-modal="toggleModal">
                <div class="text-black">
                    <h1>Como usar:</h1>
                    <p>
                        Clique na barra de pesquisa e digite o nome de sua
                        cidade
                    </p>
                </div>
            </BaseModal>
        </nav>
    </header>
</template>

<script setup>
import { ref } from "vue";
import { RouterLink, useRoute, useRouter } from "vue-router";
import BaseModal from "./BaseModal.vue";
import {uid} from 'uid'

const route = useRoute()
const router = useRouter()
const savedCities = ref([])
const addCity = ()=>{
    if(localStorage.getItem('savedCities')){
        savedCities.value = JSON.parse(localStorage.getItem("savedCities"))
    }

    const locationObj = {
        id: uid(),
        state: route.params.state,
        city: route.params.city,
        coords:{
            lat: route.query.lat,
            lon: route.query.lon
        }
    }
    savedCities.value.push(locationObj)
    localStorage.setItem("savedCities", JSON.stringify(savedCities.value))

    let query = Object.assign({}, route.query)
    delete query.preview
    query.id = locationObj.id
    router.replace({ query })
}

const modalActive = ref(false);
const toggleModal = () => {
    modalActive.value = !modalActive.value;
};
</script>