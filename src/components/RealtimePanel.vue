<script setup>
let props = defineProps(["deviceID", "temp", "humidity"])
</script>

<template>
    <div class="bg-sky-700 mx-1.5 p-6 rounded-xl border-4 w-1/4">
        <h2 class="text-2xl mb-3 font-bold text-center text-white">{{ props.deviceID}}</h2>
        <div class="flex text-center ">
            <div class="border-2 p-5 bg-green-500 relative w-6/12">
                <p class="text-sm text-center">Humidity</p>
                <p class="text-2xl pt-2">{{ props.humidity }}%</p>
            </div>
            <div class="border-2 p-5 border-l-0 bg-amber-300 relative w-6/12">
                <p class="text-sm text-center">Temperature</p>
                <p class="text-2xl pt-2">{{ props.temp }}°C</p>
            </div>
        </div>
    </div>
</template>

<style scoped></style>