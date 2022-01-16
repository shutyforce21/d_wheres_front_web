<template>
  <v-card
    class="mx-auto"
    max-width="800"
    tile
  >
    <v-img
      height="100%"
      src="https://cdn.vuetifyjs.com/images/cards/server-room.jpg"
    >
      <v-row
        align="end"
        class="fill-height"
      >
        <v-col
          align-self="start"
          class="pa-0"
          cols="12"
        >
          <v-avatar
            class="profile"
            color="grey"
            size="164"
            tile
          >
            <v-img src="https://cdn.vuetifyjs.com/images/profiles/marcus.jpg"></v-img>
          </v-avatar>
        </v-col>
        <v-col class="py-0">
          <v-list-item
            color="rgba(0, 0, 0, .4)"
            dark
          >
            <v-list-item-content>
              <v-list-item-title class="text-h6">
                Marcus Obrien
              </v-list-item-title>
              <v-list-item-subtitle>Network Engineer</v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
        </v-col>
      </v-row>
    </v-img>
  </v-card>
</template>

<script>
import axios from 'axios'
export default {
  data() {
    return {
      spot: {
        id: null,
        code: '',
        name: '',
        image: '',
        prefecture_id: '',
        address: '',
        content: '',
        location: {
          latitude: '',
          longitude: '',
        },
        available_time: {
          open_on: '',
          close_on: ''
        }
      }
    }
  },
  mounted() {
    var spotId = this.$route.query.spotId
    axios.get(`http://localhost/api/spots/${spotId}`)
    .then((res) => {
      if (res.data.message == 'success') {
        var spotData = res.data.data;
        this.spot.name = spotData.name
        this.spot.code = spotData.code
        this.spot.image = spotData.image
      } else {
        this.errMsgs = res.data.errors
      }
    })
    .catch((err) => {

    })
  },
  methods: {

  }
}
</script>

<style>
.p_img {
  border: 4px black solid;
}
</style>
