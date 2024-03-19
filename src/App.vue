<script setup>
import { onMounted, ref } from "vue";
import axios from "axios";
import LineChart from "@/components/LineChart.vue";

let parsedData;
let chartValues = ref({
    datasets: [
        {
            label: "dummy-temp-1-temperature",
            backgroundColor: "#cbf8dd",
            borderColor: "#79f8ac",
            data: [],
            parsing: {
                xAxisKey: "time",
                yAxisKey: "data.dummy-temp-1-temperature",
            },
            yAxisID: "temperatureAxis",
        }, {
            label: "dummy-temp-2-temperature",
            backgroundColor: "#cbf8dd",
            borderColor: "#79f8ac",
            data: [],
            parsing: {
                xAxisKey: "time",
                yAxisKey: "data.dummy-temp-2-temperature",
            },
            yAxisID: "temperatureAxis",
        }, {
            label: "dummy-temp-3-temperature",
            backgroundColor: "#cbf8dd",
            borderColor: "#79f8ac",
            data: [],
            parsing: {
                xAxisKey: "time",
                yAxisKey: "data.dummy-temp-3-temperature",
            },
            yAxisID: "temperatureAxis",
        }, {
            label: "dummy-temp-1-humidity",
            backgroundColor: "#fff2c6",
            borderColor: "#e8c045",
            data: [],
            parsing: {
                xAxisKey: "time",
                yAxisKey: "data.dummy-temp-1-humidity",
            },
            yAxisID: "humidityAxis",
        }, {
            label: "dummy-temp-2-humidity",
            backgroundColor: "#fff2c6",
            borderColor: "#e8c045",
            data: [],
            parsing: {
                xAxisKey: "time",
                yAxisKey: "data.dummy-temp-2-humidity",
            },
            yAxisID: "humidityAxis",
        }, {
            label: "dummy-temp-3-humidity",
            backgroundColor: "#fff2c6",
            borderColor: "#e8c045",
            data: [],
            parsing: {
                xAxisKey: "time",
                yAxisKey: "data.dummy-temp-3-humidity",
            },
            yAxisID: "humidityAxis",
        },
    ],
});
let selectedOption = ref("5m");

async function getChartData(timeRange) {
    let newData = {datasets: []};
    // Default time-range
    timeRange ??= "5m";
    let url = `http://localhost:3000/api/data?range=${timeRange}`
    console.log(url)

    await axios.get(url).then((response) => {
        let results = {};
        response.data.forEach((item) => {
            results[item._time] ??= {};
            const deviceIdAndField = item.deviceId + "-" + item._field;
            results[item._time][deviceIdAndField] = item._value;
        });
        parsedData = Object.entries(results).map(([k, data]) => {
            return {
                time: new Date(k).toLocaleString(),
                data,
            };
        });
        chartValues.value.datasets.forEach((item) => {
            newData.datasets.push({
                ...item,
                data: parsedData,
            });
        });
    });
    console.log(newData);
    chartValues.value = newData;
}

// {
//   time: 12:13pm,
//   data: {
//      dummy-1-temp: 16.21
//      dummy-1-humidity: 16.21
//      dummy-2-temp: 16.21
//      dummy-2-humidity: 16.21
//      dummy-3-temp: 16.21
//      dummy-3-humidity: 16.21
//  }
//}
getChartData();

console.log(selectedOption.value);

let chartConfig = {
    type: "line",
    responsive: true,
    maintainAspectRatio: true,
    plugins: {
        legend: {
            labels: {
                color: "rgb(26,25,25)",
            },
        },
        customCanvasBackgroundColour: {
            color: "lightGreen",
        },
    },
    scales: {
        temperatureAxis: {
            ticks: {
                callback: (value, index, values) => {
                    return `${value}°C`;
                },
            },
        },
        humidityAxis: {
            position: "right",
            grid: {
                drawOnChartArea: false,
            },
            ticks: {
                callback: (value, index, values) => {
                    return `${value}%`;
                },
            },
        },
    },
    parsing: {
        xAxisKey: "time",
    },
};

</script>

<template>
    <div class="flex">
        <select
            v-model="selectedOption"
            class="ml-auto rounded-sm border-2 border-green-400 bg-green-100 py-1.5 pl-2 pr-1.5 text-sm"
            @change="()=> getChartData(selectedOption)"
        >
            <option value="5m">5 Minutes</option>
            <option value="30m">30 Minutes</option>
            <option value="1hr">1 Hour</option>
            <option value="24hr">24 Hours</option>
        </select>
    </div>
    <LineChart :chart-data="chartValues" :chart-options="chartConfig"/>
    <!-- TODO: Continue this-->
    <button
        @click="
      () => {
        const randomNumber = Math.random();
        chartValues = {
          ...chartValues,
          datasets: [
            {
              label: 'Data One',
              data: [20, 39, 10, 40, 39, 30, 40],
            },
          ],
        };
      }
    "
    >
        click me
    </button>
</template>

<style scoped></style>
