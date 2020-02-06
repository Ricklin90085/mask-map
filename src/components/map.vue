<template>
  <l-map :zoom="zoom" :center="center">
    <l-tile-layer :url="url" :attribution="attribution"/>
      <!-- <l-marker v-for="c in storesData" :key="c.properties.id" :lat-lng="[c.geometry.coordinates[1], c.geometry.coordinates[0]]">
        <l-popup>

        </l-popup>
      </l-marker> -->
    <l-marker-cluster :options="clusterOptions">
      <l-geo-json v-for="geoJson in storesData" :key="geoJson.id" :geojson="geoJson" :options="geoJsonOptions"></l-geo-json>
    </l-marker-cluster>
    <l-marker
      v-if="myMarker.length > 0"
      :lat-lng="myMarker"
      :icon="myIcon"
    >
      <l-popup>
        你的位置
      </l-popup>
    </l-marker>
  </l-map>
</template>

<script>
/* global L */
const iconSetting = {
  iconUrl: 'https://i.imgur.com/aop9tA9.png',
  shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/0.7.7/images/marker-shadow.png',
  iconSize: [40, 41],
  iconAnchor: [12, 41],
  popupAnchor: [8, -38],
  shadowSize: [59, 41]
}

export default {
  data () {
    return {
      zoom: 13,
      center: [25.030030, 121.575002],
      url: 'https://api.mapbox.com/styles/v1/mapbox/streets-v11/tiles/{z}/{x}/{y}?access_token=pk.eyJ1Ijoicmlja2xpbjkwMDg1IiwiYSI6ImNrNjk2aXFyMTBjbTgza3BvZ29uOHc2dGcifQ.2Z0_yAGJOcfbBzYgUrgPDw',
      attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, <a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
      clusterOptions: {
        disableClusteringAtZoom: 16
      },
      storesData: [],
      myMarker: [],
      myIcon: L.icon(iconSetting),
      geoJsonOptions: {
        pointToLayer: this.createIcon,
        onEachFeature: (feature, layer) => {
          layer.bindPopup(this.popupContent(feature.properties))
        }
      },
      iconUrlList: {
        user: 'https://i.imgur.com/aop9tA9.png',
        adult: 'https://i.imgur.com/ElCfreh.png',
        child: 'https://i.imgur.com/ThnBifl.png',
        both: 'https://i.imgur.com/Wl2cA9F.png',
        none: 'https://i.imgur.com/7UXJzGr.png'
      }
    }
  },
  created () {
    this.getGeolocation()
    this.getStoresData()
  },
  mounted () {
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
        this.center = this.myMarker = [pos.coords.latitude, pos.coords.longitude]
      }, (err) => {
        console.log(err)
      })
    },
    popupContent (item) {
      return `
            <div class="mask-box">
              <div class="alert red darken-2">
                成人口罩
                <br>
                ${item.mask_adult}
              </div>
              <div class="alert blue">
                孩童口罩
                <br>
                ${item.mask_child}
              </div>
            </div>
            <span class="store-name">
              ${item.name}
            </span>
            <br>
            <span>
              電話: ${item.phone}
            </span>
            <br>
            <span>
              更新時間: ${item.updated}
            </span>
            <br>
            <a href="https://www.google.com.tw/maps/place/${item.address}" class="grey darken-4 btn" target="_blank">
              在 Google Map 開啟
            </a>
      `
    },
    createIcon (feature, latlng) {
      let prop = feature.properties
      let icon = this.iconUrlList.none

      if (prop.mask_adult >= 1 && prop.mask_child >= 1) {
        icon = this.iconUrlList.both
      } else if (prop.mask_adult >= 1 && prop.mask_child === 0) {
        icon = this.iconUrlList.adult
      } else if (prop.mask_adult === 0 && prop.mask_child >= 1) {
        icon = this.iconUrlList.child
      } else {
        icon = this.iconUrlList.none
      }

      return L.marker(latlng, {
        icon: L.icon({
          ...iconSetting,
          iconUrl: icon
        })
      })
    }
  }
}
</script>

<style lang="scss" scoped>
  .vue2leaflet-map{
    height: calc(100% - 64px) !important;
  }
  .mask-box{
    display: flex;
    margin-bottom: 5px;
    >.alert:first-child{
      margin-right: 4px;
    }
    >.alert{
      width: 50%;
    }
  }
  .leaflet-popup-content{
    font-size: 14px;
    .btn{
      display: block;
      color: #fff;
    }
  }
  .store-name{
    font-size: 18px;
    font-weight: 700;
  }
  .alert{
    font-size: 16px;
    padding: 8px 2px;
    border-radius: 5px;
    text-align: center;
    color: #fff;
  }
</style>
