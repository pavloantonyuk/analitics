<script>
import { Line } from 'vue-chartjs';

const AnalyticsChart = {
    name: 'analytics-chart',
    extends: Line,
    props: {
        labels: {
            type: Array,
            required: true,
        },
        data: {
            type: Array,
            required: true,
        },
        color: {
            type: String,
            default: '#4053b5',
        },
    },
    data() {
        return {
            options: {
                legend: {
                    display: false,
                },
                elements: {
                    line: {
                        tension: 0,
                    },
                },
                scales: {
                    yAxes: [{
                        display: false,
                    }],
                    xAxes: [{
                        gridLines: {
                            display: false,
                            tickMarkLength: 2,
                            drawBorder: false,
                        },
                        ticks: {
                            fontSize: 10,
                            fontColor: 'rgba(219,219,219,1)',
                        },
                    }],
                },
            },
        };
    },
    computed: {
        colorRGB() {
            const result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(this.color);
            return result ? {
                r: parseInt(result[1], 16),
                g: parseInt(result[2], 16),
                b: parseInt(result[3], 16),
            } : null;
        },
    },
    mounted() {
        this.renderChart({
            labels: this.labels,
            datasets: [{
                backgroundColor: `rgba(${this.colorRGB.r}, ${this.colorRGB.g}, ${this.colorRGB.b}, 0.2)`,
                borderColor: this.color,
                data: this.data,
                borderWidth: 1,
                pointRadius: 0,
            }],
        }, this.options);
    },
};
export default AnalyticsChart;
</script>
