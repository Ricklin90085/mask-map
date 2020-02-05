<template>
  <div id="app">
  </div>
</template>

<script>
import storesData from './assets/stores.json'
export default {
  name: 'app',
  data () {
    return {
      storesData
    }
  },
  mounted () {
    console.log(this.json)
  }
}
</script>

<style lang="scss">

</style>
