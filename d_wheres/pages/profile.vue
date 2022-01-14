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
            to="/updateProfile"
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
      <v-dialog
        v-if="is_self"
        transition="dialog-bottom-transition"
        max-width="600"
      >
        <template v-slot:activator="{ on, attrs }">
          <v-btn
            color="red"
            v-bind="attrs"
            v-on="on"
          >Logout</v-btn>
        </template>
        <template v-slot:default="dialog">
          <v-card>
            <v-toolbar
              color="primary"
              dark
            >Logout your account</v-toolbar>
            <v-btn @click="logout">logout</v-btn>
            <v-card-actions class="justify-end">
              <v-btn
                text
                @click="dialog.value = false"
              >Close</v-btn>
            </v-card-actions>
          </v-card>
        </template>
      </v-dialog>
    </v-row>

  </v-card>
</template>

<script>
import axios from 'axios'
export default {
  data() {
    return {
      user_code: null,
      image: require("@/assets/user.svg"),
      background: null,
      user_name: null,
      follows: 0,
      followers: 0,
      biography: '',
      genres: [],
      is_self: false,
      setting: {
        token: {},
        request_user_id: null
      },
      master: {}
    }
  },
  mounted() {
    if (this.setting.token = this.$cookies.get('token')) {
      //自身のプロフィールを閲覧する場合
      if (this.setting.request_user_id === null) {
        this.setting.request_user_id = this.$cookies.get('user_id');
      }
      axios.get(
        `http://localhost/api/profiles/${this.setting.request_user_id}`,
        { headers: { Authorization: "Bearer " + this.setting.token } }
      )
      .then((res) => {
        if (res.data.message == 'success') {
          let userData = res.data.data

          this.user_code = userData.code
          if (userData.profile.image) {
            let imagePath = 'http://localhost:80/'+ userData.profile.image
            this.image = imagePath
          }
          if (userData.profile.background) {
            let backgroundPath = 'http://localhost:80/'+ userData.profile.background
            this.background = backgroundPath
          }
          this.user_name = userData.profile.name
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
          if (res.data.message == 'success') {}
        })
        .catch((err) => {
          console.log(err);
        });
    },
    htmlText(msg){
      if( !!(msg) ){
        return msg.replace(/\r?\n/g, '<br>')
      }
    },
    logout: async function() {
      try {
        const token = this.$cookies.get('token')
        const res = await axios.get(
          "http://localhost/api/logout",
          { headers: { Authorization: "Bearer " + token } }
        )

        if (res.data.message == 'success') {
          // remove all cookies
          this.$cookies.removeAll();
          this.$router.push({ name:'map',params: { authenticated: false}})


        } else {
          this.errMsgs = res.data.errors
        }

      } catch (error) {
          console.log('post Error');
          console.error(error);
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
