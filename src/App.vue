<template>
    <div class="flex flex-col min-h-screen font-Roboto bg-weather-primary">
        <SiteNavegation />
        <RouterView v-slot="{Component}">
            <Transition name="page" mode="out-in">
                <component :is="Component" />
            </Transition>
        </RouterView>
    </div>
</template>

<script setup>
import { RouterView } from "vue-router";
import SiteNavegation from "./components/SiteNavegation.vue";
</script>

<style>
.page-enter-active,
.page-leave-active{
    transition: 250ms ease all;
}
.page-enter-from,
.page-leave-to{
    opacity: 0;
}
    ::-webkit-scrollbar{
        background: #00668A;
    }
    ::-webkit-scrollbar-thumb{
        background: #004E71;
    }
</style>