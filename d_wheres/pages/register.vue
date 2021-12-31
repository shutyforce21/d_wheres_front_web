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
        name: '',
        email: '',
        password: '',
        password_confirmation: '',
      },
      errMsgs: []
    }
  },
  methods: {
    sendForm: async function(endpoint) {
          try {
            const res = await axios.post(endpoint, this.formData)
            if (res.data.message == 'success') {
              this.$router.push('/login')

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
