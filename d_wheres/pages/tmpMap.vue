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
        >
          <v-sheet
            class="text-center mx-auto my-12"
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
                <v-chip>5:30PM</v-chip>
                <v-chip>7:30PM</v-chip>
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

    </MglMap>

      <!-- <MglGeolocateControl ref="geolocateControl" /> -->
    </client-only>
    <div class="popup" id="js-popup">
      <div class="popup-inner">
        <div class="close-btn" id="js-close-btn"><i class="fas fa-times"></i></div>

          <h4 class="text-center mt-4">Register Spot</h4>
          <v-form>
            <!-- v-model="formData.image" -->
            <v-file-input
              :rules="file_rules"
              accept="image/png, image/jpeg, image/jpg"
              placeholder="Upload your documents"
              label="File input"
              prepend-icon="mdi-paperclip"
              v-model="formData.image"
            >
              <template v-slot:selection="{ text }">
                <v-chip
                  small
                  label
                  color="primary"
                >{{ text }}</v-chip>
              </template>
            </v-file-input>

            <v-text-field
              label="Name"
              type="text"
              color="teal accent-3"
              v-model="formData.name"
            />

            <v-select
              v-model="formData.prefecture_id"
              :items="master.prefectures"
              item-text="name"
              item-value="id"
              label="Prefectures"
              style='z-index:20001;'
            ></v-select>

            <v-text-field
              label="Address"
              type="text"
              color="teal accent-3"
              v-model="formData.address"
            />
            <v-row>
              <v-col
                cols="11"
                md="5"
              >
                <v-dialog
                  ref="dialog1"
                  v-model="time_modal1"
                  :return-value.sync="formData.open_on"
                  persistent
                  width="290px"
                  style='z-index:20001;'
                >
                  <template v-slot:activator="{ on, attrs }">
                    <v-text-field
                      v-model="formData.open_on"
                      label="Open at"
                      prepend-icon="mdi-clock-time-four-outline"
                      readonly
                      v-bind="attrs"
                      v-on="on"
                    ></v-text-field>
                  </template>
                  <v-time-picker
                    v-if="time_modal1"
                    v-model="formData.open_on"
                    full-width
                  >
                    <v-spacer></v-spacer>
                    <v-btn
                      text
                      color="primary"
                      @click="time_modal1 = false"
                    >
                      Cancel
                    </v-btn>
                    <v-btn
                      text
                      color="primary"
                      @click="$refs.dialog1.save(formData.open_on)"
                    >
                      OK
                    </v-btn>
                  </v-time-picker>
                </v-dialog>
              </v-col>
              <v-spacer></v-spacer>
              <v-col
                cols="11"
                md="5"
              >
                <v-dialog
                  ref="dialog2"
                  v-model="time_modal2"
                  :return-value.sync="formData.close_on"
                  persistent
                  width="290px"
                  style='z-index:20001;'
                >
                  <template v-slot:activator="{ on, attrs }">
                    <v-text-field
                      v-model="formData.close_on"
                      label="Close at"
                      prepend-icon="mdi-clock-time-four-outline"
                      readonly
                      v-bind="attrs"
                      v-on="on"
                    ></v-text-field>
                  </template>
                  <v-time-picker
                    v-if="time_modal2"
                    v-model="formData.close_on"
                    full-width
                  >
                    <v-spacer></v-spacer>
                    <v-btn
                      text
                      color="primary"
                      @click="time_modal2 = false"
                    >
                      Cancel
                    </v-btn>
                    <v-btn
                      text
                      color="primary"
                      @click="$refs.dialog2.save(formData.close_on)"
                    >
                      OK
                    </v-btn>
                  </v-time-picker>
                </v-dialog>
              </v-col>
            </v-row>
            <v-textarea
              name="content"
              label="detail"
              auto-grow
              v-model="formData.content"
            ></v-textarea>
            <v-btn @click="sendForm()">Register</v-btn>
          </v-form>
      </div>
      <div class="black-background" id="js-black-bg"></div>
    </div>
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
      newMarker: null, //mapをクリックした時にプロットするプロットするマーカー
      location: [139.69167, 35.68944], //初期リロード時の座標
      zoom: 13,
      sheet: false,
      spotDetail: {
        id: null,
        image: {},
        name: null,
        address: null
      }, //プロットされているマーカーをクリックした時の詳細
      formData: {},
      time_modal1: false,
      time_modal2: false,
      master: {
        prefectures: []
      }, //マスタデータ
      file_rules: [
        value => !value || value.size < 500000 || 'file size should be less than 500KB!',
      ],
      setting: {
        token: {},
        request_user_id: null
      },
    };
  },
  mounted() {
    this.setting.token = this.$cookies.get('token');
    this.initializeFormData();
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
    addMarker(e) {
      this.initializeFormData()
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
    initializeFormData() {
      this.formData = {
        name: null,
        image: null,
        prefecture_id: null,
        address: null,
        content: null,
        location: {
          latitude: null,
          longitude: null
        },
        open_on: null,
        close_on: null
        // available_time: {
        //   open_on: null,
        //   close_on: null
        // }
      };
    },
    sendForm: async function() {
      this.formData.location.latitude = String(this.newMarker[1]);
      this.formData.location.longitude = String(this.newMarker[0]);
      // FormDataに入れないと送れない
      const sendFormData = new FormData();
      sendFormData.append(`image`, this.formData.image);
      sendFormData.append(`name`, this.formData.name);
      sendFormData.append(`prefecture_id`, this.formData.prefecture_id);
      sendFormData.append(`address`, this.formData.address);
      sendFormData.append(`content`, this.formData.content);
      sendFormData.append(`open_on`, this.formData.open_on);
      sendFormData.append(`close_on`, this.formData.close_on);
      sendFormData.append(`location[latitude]`, this.formData.location.latitude);
      sendFormData.append(`location[longitude]`, this.formData.location.longitude);

      let config;
      try {
        config = {headers: {
            'Authorization': "Bearer " + this.setting.token,
            'Accept': 'application/json',
            'Content-Type': 'multipart/form-data',
          }};
        const res = await axios.post('http://localhost:80/api/spots', sendFormData, config)

        if (res.data.message == 'success') {
          this.$router.push('/map');

        } else {
          console.log(this.res);
          this.errMsgs = res.data.errors
        }

      } catch (error) {
          console.log('post Error');
          console.error(error);
      }
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
  height: 1000px;
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
