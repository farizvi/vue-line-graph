<script>
import { defineComponent, h } from "vue";

import { Line } from "vue-chartjs";
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  LineElement,
  LinearScale,
  PointElement,
  CategoryScale,
} from "chart.js";
import AnnotationPlugin from "chartjs-plugin-annotation";
import { format, parseISO } from "date-fns";

import expenseData from "../data/data.json";

ChartJS.register(
  Title,
  Tooltip,
  Legend,
  LineElement,
  LinearScale,
  PointElement,
  CategoryScale,
  AnnotationPlugin
);

export default defineComponent({
  name: "LineChart",
  components: {
    Line,
  },
  props: {
    chartId: {
      type: String,
      default: "line-chart",
    },
    width: {
      type: Number,
      default: 400,
    },
    height: {
      type: Number,
      default: 400,
    },
    cssClasses: {
      default: "",
      type: String,
    },
    styles: {
      type: Object,
      default: () => {},
    },
    plugins: {
      type: Array,
      default: () => [],
    },
  },
  setup(props) {
    let resultArray = [];

    let entries = Object.entries(expenseData.outgoings);
    entries.forEach((entry) => {
      if (entry[1].length > 0) {
        let sum = entry[1].reduce((accumulator, object) => {
          return accumulator + object.amount;
        }, 0);

        resultArray.push({
          date: format(new Date(parseISO(entry[0])), "MMMM dd"),
          expense: sum,
        });
      }
    });

    console.log();

    const chartData = {
      labels: resultArray.map((r) => r.date),
      datasets: [
        {
          label: "Expenses",
          data: resultArray.map((r) => r.expense),
          borderColor: "#0064FF",
          borderWidth: 6,
          pointBackgroundColor: "#FFFFFF",
          pointBorderWidth: 2,
          pointBorderColor: "#FF0000",
          pointRadius: 8,
          pointHoverRadius: 8,
          fill: true,
          backgroundColor: (ctx) => {
            const canvas = ctx.chart.ctx;
            const gradient = canvas.createLinearGradient(0, 100, 255, 0);

            gradient.addColorStop(0, "#0064FF");
            gradient.addColorStop(0.5, "white");
            gradient.addColorStop(1, "#0064FF");

            return gradient;
          },
          tension: 0.2,
        },
      ],
    };

    const chartOptions = {
      responsive: true,
      plugins: {
        autocolors: false,
        annotation: {
          annotations: {
            line1: {
              type: "line",
              yMin: expenseData.daily_income,
              yMax: expenseData.daily_income,
              borderColor: "rgb(255, 99, 132)",
              borderWidth: 2,
              borderDash: [10, 10]
            },
          },
        },
      },
    };

    return () =>
      h(Line, {
        chartData,
        chartOptions,
        chartId: props.chartId,
        width: props.width,
        height: props.height,
        cssClasses: props.cssClasses,
        styles: props.styles,
        plugins: props.plugins,
      });
  },
});
</script>
