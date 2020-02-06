<template>
  <div id="app">
    <l-map :zoom="zoom" :center="center">
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <l-marker-cluster>
        <l-marker v-for="c in storesData" :key="c.properties.id" :lat-lng="[c.geometry.coordinates[1], c.geometry.coordinates[0]]">
          <l-popup>
            {{ c.properties.name }}
          </l-popup>
        </l-marker>
      </l-marker-cluster>
    </l-map>
  </div>
</template>

<script>
/* global L */

export default {
  name: 'app',
  data () {
    return {
      zoom: 13,
      center: L.latLng(25.033222, 121.496037),
      url: 'https://api.mapbox.com/styles/v1/mapbox/streets-v11/tiles/{z}/{x}/{y}?access_token=pk.eyJ1Ijoicmlja2xpbjkwMDg1IiwiYSI6ImNrNjk2aXFyMTBjbTgza3BvZ29uOHc2dGcifQ.2Z0_yAGJOcfbBzYgUrgPDw',
      attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, <a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
      storesData: []
    }
  },
  created () {
    this.getStoresData()
  },
  methods: {
    async getStoresData () {
      try {
        const { data } = await this.$http.get('https://raw.githubusercontent.com/kiang/pharmacies/master/json/points.json')
        this.storesData = data.features
      } catch (error) {
      }
    },
    getGeolocation () {
      if (!('geolocation' in navigator)) {
        alert('無法取得位址')
        return
      }
      // get position
      navigator.geolocation.getCurrentPosition((pos) => {
        console.log(pos)
        this.center = this.marker = L.latLng(pos.coords.latitude, pos.coords.longitude)
      }, (err) => {
        console.log(err)
      })
    }
  }
}
</script>

<style lang="scss">
@import "~leaflet.markercluster/dist/MarkerCluster.css";
@import "~leaflet.markercluster/dist/MarkerCluster.Default.css";

  html, body, #app{
    height: 100%;
    margin: 0;
  }
</style>
