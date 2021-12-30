<template>
  <div>
    <v-text-field
      v-model="formData.name"
      label="Name"
    />
    <v-text-field
      v-model="formData.email"
      label="E-mail"
    />
    <v-text-field
      v-model="formData.password"
      type="password"
      label="Password"
    />
    <v-text-field
      v-model="formData.password_confirmation"
      type="password"
      label="Password_Confirmation"
    />
    <v-btn @click="sendForm('http://localhost/api/register')">register</v-btn>
    <div v-show="this.errMsg[0]" class="errorMsgBox">
    <p class="errorMsg" v-for="(error, index) in this.errMsg[0]" :key="index">
      {{ error.message && error.message }}
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
        name: '',
        email: '',
        password: '',
        password_confirmation: '',
      },
      msg:[],
      errMsg: []
    }
  },
  methods: {
    sendForm: async function(endpoint) {
      const res = await axios.post(endpoint, this.formData)
      this.msg = res
      this.errMsg = this.msg.data.errors

    }
  }
}
</script>



<style>
.errorMsgBox {
  margin-top: 30px;
  border: solid 1px white;
  padding: 20px;
}
.errorMsg {
  color: red;
  margin-bottom: 0 !important;
}
</style>
