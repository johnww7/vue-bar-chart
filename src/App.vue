<template>
  <div id="app">
    <Chart :chartData="chartData" ></Chart>
  </div>
</template>

<script>
import Chart from './components/Chart.vue';

export default {
  name: 'App',
  components: {
    Chart,
  },
  data () {
    return {
      chartData: {},
    };
  },
  beforeMount() {
    this.getChartData();
  },
  methods: {
    async getChartData() {
      const response = await fetch("https://raw.githubusercontent.com/freeCodeCamp/ProjectReferenceData/master/GDP-data.json");
      const data = await response.json();
      console.log("API data: " + JSON.stringify(data.data.length));
      this.chartData = data;
      console.log('what: ' + JSON.stringify(this.chartData))
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
