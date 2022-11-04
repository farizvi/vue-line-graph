<template>
  <div id="app">
    <ul>
      <li v-for="point in graphPoints" :key="point.date">
        {{ point.date }} | {{ point.expense }}
      </li>
    </ul>
  </div>
</template>

<script>
import expenseData from "../data/data.json";

export default {
  name: "line-graph",
  data() {
    let resultArray = [];

    let entries = Object.entries(expenseData.outgoings);
    entries.forEach((entry) => {
      if (entry[1].length > 0) {
        let sum = entry[1].reduce((accumulator, object) => {
          return accumulator + object.amount;
        }, 0);
        resultArray.push({ date: entry[0], expense: sum });
      }
    });
    
    return {
      graphPoints: resultArray,
    };
  },
};
</script>
