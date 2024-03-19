<script setup>
import { ref } from "vue";
import axios from "axios";
import LineChart from "@/components/LineChart.vue";
import { socket } from "@/socket.js";
import RealtimePanel from "@/components/RealtimePanel.vue";

let parsedData;
let chartValues = ref({
    datasets: [
        {
            label: "dummy-1-temperature",
            backgroundColor: "#cbf8dd",
            borderColor: "#79f8ac",
            data: [],
            parsing: {
                xAxisKey: "time",
                yAxisKey: "data.dummy-1-temperature",
            },
            yAxisID: "temperatureAxis",
        }, {
            label: "dummy-2-temperature",
            backgroundColor: "#cbf8dd",
            borderColor: "#79f8ac",
            data: [],
            parsing: {
                xAxisKey: "time",
                yAxisKey: "data.dummy-2-temperature",
            },
            yAxisID: "temperatureAxis",
        }, {
            label: "dummy-3-temperature",
            backgroundColor: "#cbf8dd",
            borderColor: "#79f8ac",
            data: [],
            parsing: {
                xAxisKey: "time",
                yAxisKey: "data.dummy-3-temperature",
            },
            yAxisID: "temperatureAxis",
        }, {
            label: "dummy-1-humidity",
            backgroundColor: "#fff2c6",
            borderColor: "#e8c045",
            data: [],
            parsing: {
                xAxisKey: "time",
                yAxisKey: "data.dummy-1-humidity",
            },
            yAxisID: "humidityAxis",
        }, {
            label: "dummy-2-humidity",
            backgroundColor: "#fff2c6",
            borderColor: "#e8c045",
            data: [],
            parsing: {
                xAxisKey: "time",
                yAxisKey: "data.dummy-2-humidity",
            },
            yAxisID: "humidityAxis",
        }, {
            label: "dummy-3-humidity",
            backgroundColor: "#fff2c6",
            borderColor: "#e8c045",
            data: [],
            parsing: {
                xAxisKey: "time",
                yAxisKey: "data.dummy-3-humidity",
            },
            yAxisID: "humidityAxis",
        },
    ],
});
let panelValues = ref({
    "dummy-1-humidity": "-",
    "dummy-1-temperature": "-",
    "dummy-2-humidity": "-",
    "dummy-2-temperature": "-",
    "dummy-3-humidity": "-",
    "dummy-3-temperature": "-"
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
            item.deviceId = item.deviceId.replace("-temp", "");
            const deviceIdAndField = item.deviceId + "-" + item._field;
            results[item._time][deviceIdAndField] = item._value?.toFixed(2);
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
    chartValues.value = newData;
}

socket.on("latestData", socketData => {
    console.log("received")
    let results = {};
    socketData.forEach((item) => {
        item.deviceId = item.deviceId.replace("-temp", "");
        const deviceIdAndField = item.deviceId + "-" + item._field;
        results[deviceIdAndField] = item._value?.toFixed(2);
    });
    panelValues.value = results;

    console.log(panelValues.value);
})

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
    layout: {
      padding: 10
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
    <div class="flex justify-center font-roboto">
        <RealtimePanel
            deviceID="dummy-1"
            :temp="panelValues['dummy-1-temperature']"
            :humidity="panelValues['dummy-1-humidity']"
        />
        <RealtimePanel
            deviceID="dummy-2"
            :temp="panelValues['dummy-2-temperature']"
            :humidity="panelValues['dummy-2-humidity']"
        />
        <RealtimePanel
            deviceID="dummy-3"
            :temp="panelValues['dummy-3-temperature']"
            :humidity="panelValues['dummy-3-humidity']"
        />
    </div>
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
</template>

<style scoped></style>
