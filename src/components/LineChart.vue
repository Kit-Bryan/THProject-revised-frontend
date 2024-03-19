<template>
    <Line
        :data="chartData"
        :options="chartOptions"
    />
    {{ chartData }}
</template>

<script>

import { Line } from 'vue-chartjs'
import {
    Chart as ChartJS, CategoryScale, LinearScale, PointElement, LineElement, Title, Tooltip, Legend
} from 'chart.js'

ChartJS.register(CategoryScale, LinearScale, PointElement, LineElement, Title, Tooltip, Legend)

export default {
    props: ['chartData', 'chartOptions'],
    name: 'LineChart',
    components: {Line},
    computed: {
        updateChartData: function () {
            return this.chartData;
        },
        updateChartOptions() {
            return this.chartOptions;
        }
    }
}
</script>




