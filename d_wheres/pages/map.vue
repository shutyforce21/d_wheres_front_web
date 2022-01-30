<template>
  <div class="map">
    <client-only>
      <MglMap
        :access-token="accessToken"
        :map-style="mapStyle"
        :center="location"
        :zoom="zoom"
        @load="onMapLoaded"
      >

      <v-btn
        color="pink"
        dark
        absolute
        bottom
        right
        fab
      >
        <v-icon>mdi-plus</v-icon>
      </v-btn>

      <!-- 登録済みマーカー -->
      <div v-if="spots">
        <MglMarker v-for="spot in spots"
          :key="spot.id"
          :coordinates="spot.location"
          @click="showDetail(spot.id)"
        >
        </MglMarker>
      </div>

        <v-bottom-sheet
          class="spot-detail"
          v-model="sheet"
          inset
          background="red"
        >
          <v-sheet
            class="text-center mx-auto"
            height="600px"
          >
            <v-img
              height="250"
              :src="spotDetail.image"
            ></v-img>

            <v-card-title>{{ spotDetail.name }}</v-card-title>

            <v-card-text>
              <v-row
                align="center"
                class="mx-0"
              >
                <v-rating
                  :value="4.5"
                  color="amber"
                  dense
                  half-increments
                  readonly
                  size="14"
                ></v-rating>

                <div class="grey--text ms-4">
                  4.5 (413)
                </div>
              </v-row>
              <div style="text-align: left; ">
                <div class="my-4 text-subtitle-1">
                  $ • Italian, Cafe
                </div>
                <div class="address"><v-icon>mdi-map-marker</v-icon>{{ spotDetail.address }}</div>
              </div>
            </v-card-text>

            <v-divider class="mx-4"></v-divider>

            <v-card-title>Available time</v-card-title>

            <v-card-text>
              <v-chip-group
                active-class="deep-purple accent-4 white--text"
                column
              >
                from<v-chip>5:30PM</v-chip>
                to<v-chip>7:30PM</v-chip>
              </v-chip-group>
            </v-card-text>

            <v-btn
              class="mt-6"
              text
              color="error"
              @click="sheet = !sheet"
            >close</v-btn>

          </v-sheet>
        </v-bottom-sheet>
      </MglMap>
    </client-only>
  </div>
</template>

<script>
import Mapbox from 'mapbox-gl'
import axios from 'axios'
export default {
  data() {
    return {
      accessToken: "pk.eyJ1Ijoic2h1dHlmb3JjZSIsImEiOiJja3c3dG1ja3YxdHN6MnZtbjlobHdpYmU0In0.CUgXUng_QUDPiDEGKnRQQw",
      mapStyle: "mapbox://styles/mapbox/light-v9", // your map style
      spots: [], //mapに表示するspot一覧
      location: [139.69167, 35.68944], //初期リロード時の座標
      zoom: 13,
      sheet: false,
      spotDetail: {
        id: null,
        image: {},
        name: null,
        address: null
      }, //プロットされているマーカーをクリックした時の詳細
      master: {
        prefectures: []
      }, //マスタデータ
      setting: {
        token: {},
        request_user_id: null
      },
    };
  },
  mounted() {
    this.setting.token = this.$cookies.get('token');
    // 都道府県マスタをセット
    axios.get("http://localhost/api/master")
    .then((res) => {
      if (res.data.message == 'success') {
        this.master.prefectures = res.data.data.prefectures
      } else {
        this.errMsgs = res.data.errors
      }
    }).catch((err) => {});

    axios.get("http://localhost/api/spots")
    .then((res) => {
      if (res.data.message == 'success') {
        res.data.data.forEach(spot => {
          this.spots.push(
            {
              id: spot.id,
              name: spot.name,
              location: [
                spot.location.longitude,
                spot.location.latitude
              ],
            }
          )
        });
        console.log(this.spots);
      } else {
        this.errMsgs = res.data.errors
      }
    })
    .catch((err) => {

    })
  },
  created() {
    this.mapbox = Mapbox;
  },
  methods: {
    onMapLoaded() {
      console.log(1);
    },
    showDetail: async function(spotId) {
      const res = await axios.get(`http://localhost:80/api/spots/${spotId}`)
      let spotData = res.data.data;
      this.spotDetail.id = spotId;
      this.spotDetail.image = spotData.image;
      this.spotDetail.name = spotData.name;
      this.spotDetail.address = spotData.address;
      this.sheet = true;
      console.log(spotData);
    }
  }
};
</script>

<style scoped>
.map {
  width: 100%;
  height: 100vh;
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
/* .popup-detail {
  width: 1000px;
} */
.spot-detail {
  width: 100%;
}
.address {

}
</style>
