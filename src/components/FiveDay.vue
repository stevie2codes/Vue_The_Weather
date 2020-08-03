<template>
  <div>
    <div v-for="(data,index) in forecast" :key="index">
      <h1>{{data.date}}</h1>
      <h1>{{data.day.maxtemp_f}}</h1>
    </div>
  </div>
</template>

<script>
export default {
  name: "FiveDay",
  data() {
    return {
      forecast: []
    };
  },
  methods: {
    fetchData: async function() {
      const res = await fetch(
        `https://api.weatherapi.com/v1/forecast.json?key=484a4fd44fb94f04b3a191835200108&q=nashville&days=5`
      );
      const forecast = await res.json();
      this.forecast = forecast.forecast.forecastday;
    }
  },
  created: function() {
    this.fetchData();
  }
};
</script>

<style lang="scss" scoped>
</style>