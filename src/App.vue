<template>
  <navigation />
  <converter-currency />
</template>

<script>
import ConverterCurrency from './components/ConverterCurrency';
import Navigation from './components/Navigation';

export default {
  name: 'App',
  components: {
    ConverterCurrency,
    Navigation
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
}
</style>
