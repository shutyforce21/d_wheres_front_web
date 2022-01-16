<template>
  <v-card class="mx-auto" max-width="1000" tile>
    <v-img class="u_b_img" height="200" :src="uploadedBackgroundImg">
      <label v-show="uploadedBackgroundImgIsDefault" class="input_background_img_label"
        >画像を選択
        <input type="file" name="background" @change="onBackgroundImgChange" />
      </label>
      <v-btn
        outlined
        color="black"
        v-show="!uploadedBackgroundImgIsDefault"
        class="u_b_img_delete_btn"
        @click="removeBackgroundImg"
      >削除<v-icon right dark>mdi-trash-can-outline</v-icon></v-btn>
    </v-img>
    <v-row style="margin:2.5%;position:absolute; top: 130px">
      <v-list-item>
        <v-list-item-avatar size="100">
          <v-img class="u_img"
            :src="uploadedImg"
            alt=""
          ></v-img>
        </v-list-item-avatar>
          <div class="input-item">
            <label class="input_img_label"
              >画像を選択
              <input
                v-if="uploadedImgIsDefault"
                type="file"
                name="image"
                @change="onImgChange"
                value=''
              />
              <input
                v-else
                type="hidden"
                name="image"
                value='http://localhost:80//image/sample/user.jpg'
              />
            </label>
          </div>
          <v-btn
            outlined
            color="white"
            v-show="!uploadedImgIsDefault"
            class="u_img_delete_btn"
            @click="removeImg"
          >削除<v-icon right dark>mdi-trash-can-outline</v-icon></v-btn>
      </v-list-item>
      <v-list-item>
        <v-text-field
          id="named"
          label="name"
          name="name"
          type="name"
          color="teal accent-3"
          v-model="formData.name"
        />
      </v-list-item>
      <v-list-item>
        <v-textarea
          name="biography"
          label="Bio"
          auto-grow
          v-model="formData.biography"
        ></v-textarea>
      </v-list-item>

      <v-list-item>
        <v-list-item-content>
          <v-autocomplete
            :items="genres"
            outlined
            dense
            chips
            small-chips
            label="Genres"
            multiple
            v-model="formData.genres"
          ></v-autocomplete>
        </v-list-item-content>
      </v-list-item>
      <v-list-item>
        <v-btn
          color="primary"
          @click="sendForm('http://localhost/api/profiles')"
        >Update</v-btn>
        <v-btn
          class="cancel_btn"
          to="/profile"
        >Cancel</v-btn>
      </v-list-item>
    </v-row>

  </v-card>
</template>

<script>
import axios from 'axios'
export default {
  data() {
    return {
      formData: {
        background: {},
        image: {},
        name: '',
        biography: '',
        genres: []
      },
      uploadedBackgroundImg: {},
      uploadedBackgroundImgIsDefault: true,
      uploadedImg: require("@/assets/user.svg"),
      uploadedImgIsDefault: true,
      img_name: '',
      genres: [
        'asfd1',
        'asfd2',
        'asfd3',
        'asfd4'
      ],
      setting: {
        token: {},
        user_id: null
      },
    };
  },
  mounted() {
    this.setting.token = this.$cookies.get('token');
    this.setting.user_id = this.$cookies.get('user_id');
    axios.get(
        `http://localhost/api/profiles/${this.setting.user_id}`,
        { headers: { Authorization: "Bearer " + this.setting.token } }
      )
      .then((res) => {
        if (res.data.message == 'success') {
          let userData = res.data.data
          if (userData.profile.image) {
            // console.log(require('http://localhost:80/'+ userData.profile.image));
            this.uploadedImg = 'http://localhost:80/'+ userData.profile.image
          }
          if (userData.profile.background) {
            this.uploadedBackgroundImg = 'http://localhost:80/'+ userData.profile.background
          }
          this.formData.name = userData.profile.name
          this.formData.biography = userData.profile.biography
          // this.genres = userData.profile.genres
        }
      })
      .catch((err) => {
        console.log(err);
      });
  },
  methods: {
    onBackgroundImgChange(e) {
      const files = e.target.files || e.dataTransfer.files;
      this.showBackgroundImg(files[0]);
      this.formData.background = files[0];
      this.uploadedBackgroundImgIsDefault = false;
    },
    showBackgroundImg(file) {
      const reader = new FileReader();
      reader.onload = e => {
        this.uploadedBackgroundImg = e.target.result;
      };
      reader.readAsDataURL(file);
    },
    removeBackgroundImg() {
      this.uploadedBackgroundImg = '';
      this.uploadedBackgroundImgIsDefault = true;
    },

    onImgChange(e) {
      const files = e.target.files || e.dataTransfer.files;
      this.showImg(files[0]);
      this.formData.image = files[0];
      this.uploadedImgIsDefault = false;
    },
    // アップロードした画像を表示
    showImg(file) {
      const reader = new FileReader();
      reader.onload = e => {
        this.uploadedImg = e.target.result;
      };
      reader.readAsDataURL(file);
    },
    removeImg() {
      this.uploadedImg = require("@/assets/user.svg");
      this.uploadedImgIsDefault = true;
    },

    sendForm: async function(endpoint) {
      // デフォ画像ならnullをセット
      if (this.uploadedImgIsDefault === true) {
        this.formData.image = null;
      }
      if (this.uploadedBackgroundImgIsDefault === true) {
        this.formData.background = null;
      }
      console.log(this.formData)
      try {
        let config = {headers: {'content-type': 'multipart/form-data'}};
        const res = await axios.put(endpoint, this.formData, config)
        console.log(res);
        if (res.data.message == 'success') {
          this.$router.push({name: 'profile'})
        } else {
          this.errMsgs = res.data.errors
        }

      } catch (error) {
          console.log('post Error');
          console.error(error);
      }
    }
  },
}
</script>

<style>
.u_b_img {
  background: linear-gradient( darkgray, #626263);
  /* text-align:center; */
  position:relative;
}
.u_img {
  border: 4px black solid;
  background-color: darkgray;
}
.bio {
  /* 改行コード有効化 */
  white-space: pre-wrap;
}
.input_img_label > input {
  display: none;
}

.input_img_label {
  padding: 0 1rem;
  border: solid 1px #888;
}

.input_img_label::after {
  content: '+';
  font-size: 1rem;
  color: #888;
  padding-left: 1rem;
}

.input_background_img_label > input {
  display: none;
}

.input_background_img_label {
  padding: 0 1rem;
  border: solid 1px rgb(63, 62, 62);
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  -webkit-transform: translate(-50%, -50%);
  -ms-transform: translate(-50%, -50%);
}

.input_background_img_label::after {
  content: '+';
  font-size: 1rem;
  color: rgb(88, 88, 88);
  padding-left: 1rem;
}
.u_b_img_delete_btn {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  -webkit-transform: translate(-50%, -50%);
  -ms-transform: translate(-50%, -50%);
}
.cancel_btn {
  margin-left: 20px;
}
</style>
