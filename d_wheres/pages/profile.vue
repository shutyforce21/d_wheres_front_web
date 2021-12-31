<template>
  <v-card class="mx-auto" max-width="1000" tile>
    <v-img height="200" src="https://cdn.vuetifyjs.com/images/cards/server-room.jpg"></v-img>
    <v-row style="margin:2.5%;position:absolute; top: 130px">
      <v-list-item>
        <v-list-item-avatar size="100">
          <img class="p_img" src="https://www.w3schools.com/howto/img_avatar.png" alt="John">
        </v-list-item-avatar>
        <v-list-item-content>
          <v-list-item-title class="title" style="margin-top:20px;">{{ user_name }}</v-list-item-title>
          <v-list-item-subtitle>{{ user_code }}</v-list-item-subtitle>
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
      user_name: 'Sample Name',
      user_code: '@Asdf1234',
    }
  },
  mounted() {
    // try {
    //       const res = await axios.post(endpoint, this.formData)
    //       if (res.data.message == 'success') {
    //         this.$router.push('/login')

    //       } else {
    //         this.errMsgs = res.data.errors
    //       }

    //     } catch (error) {
    //         console.log('post Error');
    //         console.error(error);
    //     }
  },
  methods: {
    sendForm: async function(endpoint) {

    }
  }
}
</script>

<style>
.p_img {
  border: 4px black solid;
}
</style>
