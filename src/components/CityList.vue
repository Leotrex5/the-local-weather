<template>
    <div v-for="city in savedCities" :key="city.id"> 
        <CityCard :city="city" @click="goToCityView(city)"/>
    </div>

    <p v-if="savedCities.length === 0">
        Nenhuma cidade sendo monitorada no momento, procure algumas no campo acima.
    </p>
</template>

<script setup>
import router from "@/router";
import axios from "axios";
import { ref } from "vue";
import CityCard from "../components/CityCard.vue"


const APIkey = "dcd521bfb04e047565cf9bd29657441c"
const savedCities = ref([]);
const getCities = async () => {
    if(localStorage.getItem('savedCities')){
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'))

        const requests = []
        savedCities.value.forEach((city)=>{
            requests.push(
                axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lon}&appid=${APIkey}&lang=pt_br&units=metric`)
            )     
        })
        const weatherData = await Promise.all(requests)

        await new Promise((res)=> setTimeout(res,500))

        weatherData.forEach((value,index)=>{
            savedCities.value[index].weather = value.data
        })
    }
}
await getCities()

const goToCityView = (city)=>{
    router.push({
        name:'cityView',
        params:{
            state:city.state,
            city:city.city,
        },
        query:{
            lat: city.coords.lat,
            lon: city.coords.lon,
            id: city.id,
        }
    })
}
</script>