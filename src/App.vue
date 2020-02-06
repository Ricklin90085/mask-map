<template>
  <div id="app">
    <nav>
      <div class="nav-wrapper teal lighten-3">
        <a href="#" class="brand-logo">
          口罩地圖
        </a>
      </div>
    </nav>
    <Map />
  </div>
</template>

<script>
import Map from './components/map'
export default {
  name: 'app',
  components: {
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
  nav .brand-logo{
    left: 10px;
  }
</style>
