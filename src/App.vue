<script setup>
import { ref } from "vue";
import LineChart from "@/components/LineChart.vue";

let chartValues = ref({
    labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July'],
    datasets: [
        {
            label: 'Data One',
            data: [40, 39, 10, 40, 39, 80, 40]
        }
    ]
})

let myDatasets = [
    [
        {
            label: 'dummy-temp-1',
            data: [20, 20, 20, 20, 20, 20, 20]
        }
    ], [
        {
            label: 'dummy-temp-2',
            data: [20, 20, 30, 20, 20, 40, 20]
        }
    ], [
        {
            label: 'dummy-temp-3',
            data: [20, 60, 20, 80, 20, 10, 20]
        }
    ],
]
</script>

<template>
    <LineChart :chart-data="chartValues" :chart-options=" {responsive: true,maintainAspectRatio: true}"/>
    <!-- TODO: Continue this-->
    <button @click="()=>{
        const randomNumber = Math.random()
        chartValues = {...chartValues, datasets: myDatasets[Math.floor(Math.random()*3)]
        }
    }
"> click me
    </button>
</template>

<style scoped>

</style>
