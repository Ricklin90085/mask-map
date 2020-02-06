<template>
  <div id="app">
    <Nav />
    <Map />
  </div>
</template>

<script>
import Nav from './components/nav'
import Map from './components/map'
export default {
  name: 'app',
  components: {
    Nav,
    Map
  }
}
</script>

<style lang="scss">
  *{
    box-sizing: border-box;
  }
  html, body, #app{
    height: 100%;
    margin: 0;
  }
</style>
