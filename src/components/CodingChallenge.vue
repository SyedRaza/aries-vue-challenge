<template>
  <div>
    <h1>Options Profit Calculator</h1>
      <div class="options-calculator">
          <LineChart :data="chartData" :options="options" />
      </div>
  </div>
</template>

<script>
import {
    Chart as ChartJS,
    Title,
    Tooltip,
    Legend,
    LineElement,
    PointElement,
    CategoryScale,
    LinearScale,
    Filler,
} from 'chart.js';
import { Line as LineChart } from 'vue-chartjs';
import { calculateData } from "@/utils/process";
ChartJS.register(CategoryScale, LinearScale, LineElement, PointElement, Filler, Title, Tooltip, Legend);

export default {
    name: 'CodingChallenge',
    components: { LineChart },
    props: {
        optionsData: {
            type: Array,
            default: () => [],
            validator: (options) => options.length <= 4
        }
    },
    data() {
        return {
            chartData: null,
            options: {
                responsive: true,
                scales: {
                    y: {
                        beginAtZero: true,
                        title: {
                            display: true,
                            text: 'Risk & Reward Graph'
                        }
                    },
                    x: {
                        stacked: true,
                    }
                },
                layout: {
                    padding: 20
                }
            }
        };
    },
    mounted() {
        this.updateChartData();
    },
    methods: {
        updateChartData() {
            const data = this.optionsData.map(options => calculateData(options));
            this.chartData = {
                labels: this.optionsData.map(it => `${it.strike_price}(${it.type})`),
                datasets: [
                    {
                        label: "Maximum Loss",
                        borderColor: "#FF6384",
                        backgroundColor: 'rgba(255, 99, 132, 0.2)',
                        data: data.map(it => it.maxLoss),
                        fill: true
                    },
                    {
                        label: "Maximum Profit",
                        borderColor: "#36A2EB",
                        backgroundColor: 'rgba(54, 162, 235, 0.2)',
                        data: data.map(it => it.maxProfit),
                        fill: true
                    },
                    {
                        label: "Break Even Point",
                        borderColor: "#FFCE56",
                        backgroundColor: 'rgba(255, 206, 86, 0.2)',
                        data: data.map(it => it.breakEvenPoint),
                        fill: true
                    }
                ]
            };
        }
    },
    watch: {
        optionsData: {
            deep: true,
            handler() {
                this.updateChartData();
            },
            immediate: true
        }
    }
}
</script>

<style scoped>
.options-calculator {
    max-width: 600px;
    margin: auto;
    text-align: left;
    padding: 20px;
    box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}

h1 {
    color: #333;
}

</style>
