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

      <!-- 登録済みマーカー -->
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

      <!-- 登録用マーカー -->
      <div v-if="newMarker">
        <MglMarker
        :coordinates="newMarker"
      >
        <MglPopup
          anchor="top"
        >
          <VCard>
            <h2>ここに練習場所を追加する</h2>
            <v-btn id='js-show-popup' @click="showPopup()">Show Popup</v-btn>
          </VCard>
        </MglPopup>
      </MglMarker>
      </div>

      <MglGeolocateControl ref="geolocateControl" />
      </MglMap>
    </client-only>
    <div class="popup" id="js-popup">
      <div class="popup-inner">
        <div class="close-btn" id="js-close-btn"><i class="fas fa-times"></i></div>

          <h4 class="text-center mt-4">Register Spot</h4>
          <v-form>
            <v-text-field
              label="Email"
              name="Email"
              prepend-icon="mdi-email"
              type="text"
              color="teal accent-3"
            />

            <v-text-field
              id="password"
              label="Password"
              name="password"
              prepend-icon="mdi-lock-check"
              type="password"
              color="teal accent-3"
            />

            {{ newMarker }}
            <v-btn @click="sendForm()">Register</v-btn>
          </v-form>
      </div>
      <div class="black-background" id="js-black-bg"></div>
    </div>
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
      newMarker: null,
      location: [139.69167, 35.68944],
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
      this.newMarker = [this.lngLat.lng, this.lngLat.lat];
    },
    showPopup() {
      var popup = document.getElementById('js-popup');
      if(!popup) return;
      popup.classList.add('is-show');

      var blackBg = document.getElementById('js-black-bg');
      var closeBtn = document.getElementById('js-close-btn');

      closePopUp(blackBg);
      closePopUp(closeBtn);

      function closePopUp(elem) {
        if(!elem) return;
        elem.addEventListener('click', function() {
          popup.classList.remove('is-show');
        })
      }
    },
    sendForm() {
      console.log('success');
    }
  }
};
</script>

<style scoped>
.map {
  width: 100%;
  height: 500px;
}
.popup {
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  z-index: 9999;
  opacity: 0;
  visibility: hidden;
  transition: .6s;
}
.popup.is-show {
  opacity: 1;
  visibility: visible;
}
.popup-inner {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%,-50%);
  width: 80%;
  max-width: 600px;
  padding: 50px;
  background-color: rgb(41, 36, 36);
  z-index: 2;
}
.popup-inner img {
  width: 100%;
}
.close-btn {
  position: absolute;
  right: 0;
  top: 0;
  width: 50px;
  height: 50px;
  line-height: 50px;
  text-align: center;
  cursor: pointer;
}
.close-btn i {
  font-size: 20px;
  color: #333;
}
.black-background {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(63, 60, 60, 0.8);
  z-index: 1;
  cursor: pointer;
}

</style>
