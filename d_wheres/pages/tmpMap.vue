<template>
  <div class="map">
    <client-only>
      <MglMap
        :access-token="accessToken"
        :map-style="mapStyle"
        :center="location"
        :zoom="zoom"
        @load="onMapLoaded"
        @click="addMarker"
      >
      <MglMarker v-for="(s, key) in defSpots"
        :key="key"
        :coordinates="s.location"
      >
        <MglPopup>
          <VCard>
            <div>{{ s.name }}</div>
            <div>{{ s.description }}</div>
          </VCard>
        </MglPopup>
      </MglMarker>

      <div v-if="location2">
        <MglMarker
        :coordinates="location2"
      >
        <!-- 登録ずみ -->
        <MglPopup>
          <VCard>
            <p>new</p>
          </VCard>
        </MglPopup>
      </MglMarker>
      </div>

      <MglGeolocateControl ref="geolocateControl" />
      </MglMap>
    </client-only>
  </div>
</template>
<script>
import Mapbox from 'mapbox-gl'

export default {
  data() {
    return {
      accessToken: "pk.eyJ1Ijoic2h1dHlmb3JjZSIsImEiOiJja3c3dG1ja3YxdHN6MnZtbjlobHdpYmU0In0.CUgXUng_QUDPiDEGKnRQQw",
      mapStyle: "mapbox://styles/mapbox/light-v9", // your map style
      defSpots: [
        {
          location: [139.69167, 35.68944],
        },
        {
          location: [139.69138, 35.68925],
        }
      ],
      newSpot: [],
      lngLat: {},
      location: [139.69167, 35.68944],
      location2: null,
      zoom: 13,
    };
  },
  created() {
    this.mapbox = Mapbox;
  },
  methods: {
    onMapLoaded() {
      console.log(1);
    },
    addMarker(e) {
      this.lngLat = e.mapboxEvent.lngLat
      // this.newSpot = [this.lngLat.lat, this.lngLat.lng];
      this.location2 = [this.lngLat.lng, this.lngLat.lat];
    },
  }
};
</script>

<style scoped>
.map {
  width: 100%;
  height: 500px;
}
.popup {
  width: 200px;
  height: 100px;
}

</style>
