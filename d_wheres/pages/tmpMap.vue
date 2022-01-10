<template>
  <MglMap :accessToken="accessToken" :mapStyle="mapStyle" />
</template>

<script>
import Mapbox from "mapbox-gl";
import { MglMap } from "vue-mapbox";

export default {
  components: {
    MglMap
  },
  data() {
    return {
      accessToken: "pk.eyJ1Ijoic2h1dHlmb3JjZSIsImEiOiJja3c3dG1ja3YxdHN6MnZtbjlobHdpYmU0In0.CUgXUng_QUDPiDEGKnRQQw",
      // mapStyle: MAP_STYLE
    };
  },

  created() {
    this.mapbox = Mapbox;
  }
};
</script>

<style>
#map{
   width:100%;
   height:100vh;
}
</style>
