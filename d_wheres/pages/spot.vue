<template>
  <v-card class="mx-auto" max-width="1000" tile>
    <v-img height="200" src="https://cdn.vuetifyjs.com/images/cards/server-room.jpg"></v-img>
    <v-row style="margin:2.5%;position:absolute; top: 130px">
      <v-list-item>
        <v-list-item-avatar size="100">
          <img class="p_img" :src="spot.image">
        </v-list-item-avatar>
        <v-list-item-content>
          <v-list-item-title class="title" style="margin-top:20px;">{{ spot.name }}</v-list-item-title>
          <v-list-item-subtitle>{{ spot.code }}</v-list-item-subtitle>
        </v-list-item-content>
        <v-btn
          class="ma-2"
          outlined
          color="white"
          to="/map"
        >Edit profile</v-btn>
      </v-list-item>
    </v-row>
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
