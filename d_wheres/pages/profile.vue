<template>
  <v-card class="mx-auto" max-width="1000" tile>
    <v-img class="u_b_img" height="200" :src="background"></v-img>
    <v-row style="margin:2.5%;position:absolute; top: 130px">
      <v-list-item>
        <v-list-item-avatar size="100">
          <v-img class="u_img" :src="image" alt="John"></v-img>
        </v-list-item-avatar>
        <v-list-item-content>
          <v-list-item-title class="title" style="margin-top:20px;">{{ user_name }}</v-list-item-title>
          <v-list-item-subtitle>{{ user_code }}</v-list-item-subtitle>
        </v-list-item-content>
        <div v-if="is_self">
          <v-btn
            class="ma-2"
            outlined
            color="white"
            to="/map"
          >
          <v-icon>mdi-account-cog</v-icon>
          Edit profile</v-btn>
        </div>
      </v-list-item>
      <v-list-item>
        <v-list-item-content class="bio">
          {{ biography }}
        </v-list-item-content>
      </v-list-item>

      <v-list-item two-line>
        <v-list-item-content>
          <v-list-item-title @click="getFollows()"><button>{{ follows }} follow</button></v-list-item-title>
          <v-list-item-title @click="getFollowers()"><button>{{ followers }} follower</button></v-list-item-title>
        </v-list-item-content>
      </v-list-item>

      <v-list-item>
        <v-list-item-content>
          <v-list-item-title>Genres</v-list-item-title>
          <v-list-item-subtitle
            v-for="(g, i) in genres"
            :key="i"
          >
            {{ g }}
          </v-list-item-subtitle>
        </v-list-item-content>
      </v-list-item>
    </v-row>

  </v-card>
</template>

<script>
import axios from 'axios'
export default {
  data() {
    return {
      user_name: null,
      user_code: null,
      image: require("@/assets/user.svg"),
      background: null,
      follows: 0,
      followers: 0,
      biography: '',
      genres: [],
      is_self: false,
      setting: {
        authJson: {},
        user_id: null
      },
      master: {}
    }
  },
  mounted() {
    if (localStorage.getItem("authentication")) {

      this.setting.authJson = JSON.parse(localStorage.getItem("authentication"));
      //自身のプロフィールを閲覧する場合
      if (this.setting.user_id === null) {
        this.setting.user_id = this.setting.authJson.user_id
      }
      axios.get(
        `http://localhost/api/profiles/${this.setting.user_id}`,
        { headers: { Authorization: "Bearer " + this.setting.authJson.auth_token } }
      )
      .then((res) => {
        if (res.data.message == 'success') {
          let userData = res.data.data
          this.user_name = userData.name
          this.user_code = userData.code
          this.image = userData.profile.image
          this.background = userData.profile.background
          this.biography = userData.profile.biography
          this.follows = userData.profile.follows
          this.followers = userData.profile.followers
          this.genres = userData.profile.genres
          this.is_self = userData.is_self
        }
      })
      .catch((err) => {
        console.log(2);
        console.log(err);
      });
    }
  },
  methods: {
    getFollows() {
      axios.get(
          'http://localhost/api/user/follows',
          { headers: { Authorization: "Bearer " + this.setting.authJson.auth_token } }
        )
        .then((res) => {
          if (res.data.message == 'success') {
            let userData = res.data.data
            console.log(userData)
            this.user_name = userData.name
            this.user_code = userData.code
            this.image = userData.profile.image
            this.background = userData.profile.background
            this.follows = userData.profile.follows
            this.followers = userData.profile.followers
          }
        })
        .catch((err) => {
          console.log(err);
        });
    },
    htmlText(msg){
      if( !!(msg) ){
        return msg.replace(/\r?\n/g, '<br>')
      }
    }
  }
}
</script>

<style>
.u_b_img {
  background: linear-gradient( darkgray, #626263);
}
.u_img {
  border: 4px black solid;
  background-color: darkgray;
}
.bio {
  /* 改行コード有効化 */
  white-space: pre-wrap;
}
</style>
