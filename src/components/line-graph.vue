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
import { format, parseISO} from "date-fns";

import expenseData from "../data/data.json";

ChartJS.register(
  Title,
  Tooltip,
  Legend,
  LineElement,
  LinearScale,
  PointElement,
  CategoryScale
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
        
        resultArray.push({ date: format(new Date(parseISO(entry[0])), "MMMM dd"), expense: sum });
      }
    });

    const chartData = {
      labels: resultArray.map((r) => r.date),
      datasets: [
        {
          label: "Expenses",
          data: resultArray.map((r) => r.expense),
          borderColor: 'rgb(75, 192, 192)',          
        },
      ],
    };

    const chartOptions = { responsive: true };

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
