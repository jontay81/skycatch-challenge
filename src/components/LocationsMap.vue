<template lang="html">
  <div>
    <p v-if="center"> Lat: {{ center.lat }}, lon {{ center.lng }} </p>
    <div id="map">
    </div>
  </div>
</template>

<script>
import 'leaflet'
import 'leaflet.markercluster'

const L = window.L;
export default {
  data() {
    return {
      map: [],
      markers: null
    }
  },
  computed: {
    places() {
      return this.$store.state.places
    },
    center() {
      return this.$store.state.locationsMap.center
    }
  },
  watch: {
    places(val) {
      this.removePlaces()
      this.addPlaces(val)
    },
    center(location) {
      this.map.setView([location.lat, location.lng], location.zoom)
    }
  },
  methods: {
    addPlaces(places) {
      if(!this.map) return;
      const map = this.map
      const markers = L.markerClusterGroup();
      const store = this.$store;

      places.forEach( (place) => {
        let marker = L.marker([place.location.lat, place.location.lng])
            .on('click', (el) => {
              store.commit('locationsMap_center', el.latlng)
            })
            .bindPopup(`<b> ${place.id} </b> ${place.name}`)
        markers.addLayer(marker)
        // .on('click', (el) => alert(el.target))
      })

      map.addLayer(markers)
      this.markers = markers
    },
    removePlaces() {
      this.map.removeLayer(this.markers)
      this.markers = null
    }
  },
  mounted() {
    const map = L.map('map').setView([37.4501001, -121.9107704], 4)

    L.tileLayer('https://{s}.tiles.mapbox.com/v4/{user}.{mapId}/{z}/{x}/{y}.png?access_token={token}', {
      attribution: 'Map data &copy; <a href="http://openstreetmap.org">OpenStreetMap</a> contributors, <a href="http://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, Imagery © <a href="http://mapbox.com">Mapbox</a>',
      maxZoom: 18,
      mapId: 'i81oam9h',
      user: 'skycatch-dev',
      token: 'pk.eyJ1Ijoic2t5Y2F0Y2gtZGV2IiwiYSI6Ik1PVjVYNEkifQ.j2X9OOZDz7ABqUvHk4kesw'
    }).addTo(map)
    this.map = map
    this.addPlaces(this.places)
  }
}
</script>

<style lang="sass">
@import "../../node_modules/leaflet/dist/leaflet.css"
@import "../../node_modules/leaflet.markercluster/dist/MarkerCluster.css"


#map
  width: 100%
  height: 400px
  font-weight: bold
  font-size: 13px
  text-shadow: 0 0 2px #fff
  .leaflet-shadow-pane > .leaflet-marker-shadow
    display: none
</style>
