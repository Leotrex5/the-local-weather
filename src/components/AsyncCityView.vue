<template>
    <div class="flex flex-col flex-1 items-center">
        <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
            <p>Você está apenas visualizando a cidade no momento, clique no "+" para começar a acompanhar esta cidade.</p>
        </div>

        <div class="flex flex-col items-center text-white py-12">
            <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
            <p class="text-sm mb-12">
                {{
                    new Date(weatherData.currentTime).toLocaleDateString(
                        "pt-br",
                        {
                            weekday:"long",
                            day:"2-digit",
                            month:"long",
                        }
                    )
                }}
                {{
                    new Date(weatherData.currentTime).toLocaleTimeString(
                        "pt-br",
                        {
                            timeStyle:"short"
                        }
                    )
                }}
            </p>
            <p class="text-6xl">{{Math.round(weatherData.main.temp)}}&deg;</p>
                <p>Sensação de {{Math.round(weatherData.main.feels_like)}}&deg;</p>
                <p class="capitalize">{{weatherData.weather[0].description}}</p>
                <img class="w-[150px] h-auto"
                :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`" alt="">
        </div>

        <hr class="border-white border-opacity-10 border w-full">

        <!-- hourly weather -->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="mb-4">Hourly Weather</h2>
                <div class="flex gap-10  overflow-x-auto">
                    <div v-for="hour in weatherHourly.list" :key="hour.dt"
                        class="flex flex-col gap-4 items-center"
                    >
                        <p class="whitespace-nowrap text-md"
                        >{{new Date(hour.currentTime)
                            .toLocaleDateString(
                                "pt-br",{
                                    hour:"numeric",
                            }
                            )
                        }}</p>

                        <img class="w-auto h-[50px] object-cover"
                        :src="`http://openweathermap.org/img/wn/${hour.weather[0].icon}@2x.png`" alt="">

                        <p class="text-xl">{{Math.round(hour.main.temp)}}&deg;</p>

                    </div>
                </div>
            </div>
        </div>

        <hr class="border-white border-opacity-10 border w-full">
        <!-- weekly weather -->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="mb-4">7 Day Forecast</h2>
                <div class="flex items-center"
                v-for="day in weatherHourly.list" :key="day.id">
                    <p class="flex-1">
                        {{new Date(day.currentTime).toLocaleDateString("pt-br",{weekday:"long"})}}
                    </p>
                    <img :src="`http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`" alt="">
                    <div class="flex gap-2 flex-1 justify-end">
                        <p>Máx: {{Math.round(day.main.temp_max)}} </p>
                        <p>Min: {{Math.round(day.main.temp_min)}} </p>
                    </div>
                </div>
            </div>
        </div>

        <div class="flex items=center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500" @click="removeCity">
            <i class="fa-solid fa-trash"></i>
            <p>Remover Cidade</p>
        </div>
    </div>
</template>

<script setup>
import axios from "axios";
import { useRoute, useRouter } from "vue-router";

const APIkey = "dcd521bfb04e047565cf9bd29657441c"
const route = useRoute()
const router = useRouter()

const getWeatherData =  async ()=>{
    try{
        const response = await axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${route.query.lat}&lon=${route.query.lon}&appid=${APIkey}&lang=pt_br&units=metric`)

        //call current date & time
        const localOffset = new Date().getTimezoneOffset() * 60000
        const utc = response.data.dt * 1000 + localOffset
        response.data.currentTime = utc + 1000 * response.data.timezone

        await new Promise((res)=> setTimeout(res,500))

        return response.data

    }catch(err){
        console.log(err)
    }
}
const weatherData = await getWeatherData();

const getWeatherHourly = async ()=>{
    try{
        const response = await axios.get(`https://api.openweathermap.org/data/2.5/forecast?lat=${route.query.lat}&lon=${route.query.lon}&appid=${APIkey}&lang=pt_br&units=metric`)

        // cal hourly weather offset
        const localOffset = new Date().getTimezoneOffset() * 60000

        response.data.list.forEach((response)=>{
            const utc = response.dt * 1000 + localOffset
            response.currentTime = utc + 1000 * weatherData.timezone
        })

        return response.data

    }catch(err){
        console.log(err)
    }
}
const weatherHourly = await getWeatherHourly();

const removeCity = ()=>{
    const cities = JSON.parse(localStorage.getItem("savedCities"))
    const updatedCities = cities.filter(
        (city)=> city.id !== route.query.id
    )
    localStorage.setItem('savedCities', JSON.stringify(updatedCities))

    router.push({
        name: "home"
    })
}
</script>
