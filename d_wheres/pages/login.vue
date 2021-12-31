<template>
  <div>
    <v-text-field
      v-model="formData.email"
      label="E-mail"
    />
    <v-text-field
      v-model="formData.password"
      type="password"
      label="Password"
    />
    <v-btn @click="sendForm('http://localhost/api/login')">login</v-btn>
    <div v-show="this.errMsgs" class="errorMsgBox">
    <p class="errorMsg" v-for="(msg, index) in this.errMsgs" :key="index">
      {{ msg.message && msg.message }}
    </p>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data() {
    return {
      formData: {
        email: '',
        password: '',
      },
      errMsgs: []
    }
  },
  methods: {
    sendForm: async function(endpoint) {
          try {
            const res = await axios.post(endpoint, this.formData)
            if (res.data.message == 'success') {

              //ストレージ削除
              localStorage.clear();
              let values = {
                auth_token : res.data.data.token,
                user_name : res.data.data.name,
                user_id : res.data.data.user_id
              }
              // DevTools/Application/LocalStorageにObjectで保存
              localStorage.setItem('authentication', JSON.stringify(values));

              this.$router.push({
                name: 'profile',
              })

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
.errorMsg {
  color: red;
  margin-bottom: 0 !important;
}
</style>
