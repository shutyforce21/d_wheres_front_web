<template>
  <div class="map">
    <client-only>
      <MglMap
        :access-token="accessToken"
        :map-style="mapStyle"
        :center="location"
        :zoom="zoom"
      >
        <MglGeolocateControl ref="geolocateControl" />
      </MglMap>
    </client-only>
  </div>
</template>
<script>
import Mapbox from 'mapbox-gl';
export default {
  data() {
    return {
      accessToken: "pk.eyJ1Ijoic2h1dHlmb3JjZSIsImEiOiJja3c3dG1ja3YxdHN6MnZtbjlobHdpYmU0In0.CUgXUng_QUDPiDEGKnRQQw",
      mapStyle: "mapbox://styles/mapbox/light-v9", // your map style
      location: [-111.549668, 39.014],
      zoom: 13,
    };
  },

  created() {
    // We need to set mapbox-gl library here in order to use it in template
    this.mapbox = Mapbox;
  }
};
</script>
<style scoped>
.map {
  width: 100%;
  height: 500px;
}

</style>
