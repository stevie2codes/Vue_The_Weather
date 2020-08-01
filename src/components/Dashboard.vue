<template>
  <div>
    <form @submit.prevent="processInput">
      <input type="text" v-model="input" name="input" placeholder="Enter a location" />
      <button type="submit">
        <v-icon name="search" class="searchIcon" />
      </button>
    </form>
    <h1>{{currentWeather}}</h1>
  </div>
</template>

<script>
export default {
  name: "Dashboard",

  data() {
    return {
      currentWeather: {},
      input: ""
    };
  },

  methods: {
    fetchData: async function() {
      const res = await fetch(
        `http://api.weatherapi.com/v1/current.json?key=484a4fd44fb94f04b3a191835200108&q=${this.input}`
      );
      const currentWeather = await res.json();
      this.currentWeather = currentWeather;
    },
    processInput: function() {
      this.fetchData();
    }
  }
};
</script>

<style lang="scss" scoped>
</style>