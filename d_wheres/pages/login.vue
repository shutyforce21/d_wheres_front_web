<template>
    <v-container class="fill-height" fluid>
      <v-row align="center" justify="center">
        <v-col cols="12" sm="8" md="8">
          <v-card class="elevation-12">
            <v-window v-model="step">
              <v-window-item :value="1">
                <v-row>
                  <v-col cols="12" md="8">
                    <v-card-text class="mt-12">
                      <h1
                        class="text-center display-2 teal--text text--accent-3"
                      >Sign in to dWheres</h1>
                      <h4 class="text-center mt-4">Ensure your email for login</h4>
                      <v-form>
                        <v-text-field
                          label="Email"
                          name="Email"
                          prepend-icon="mdi-email"
                          type="text"
                          color="teal accent-3"
                        />

                        <v-text-field
                          id="password"
                          label="Password"
                          name="password"
                          prepend-icon="mdi-lock-check"
                          type="password"
                          color="teal accent-3"
                        />
                      </v-form>
                      <h3 class="text-center mt-4">Forgot your password ?</h3>
                    </v-card-text>
                    <div class="text-center mt-3">
                      <v-btn rounded color="teal accent-3" dark>SIGN IN</v-btn>
                    </div>
                  </v-col>
                  <v-col cols="12" md="4" class="teal accent-3">
                    <v-card-text class="white--text mt-12">
                      <h1 class="text-center display-1">Hello, Friend!</h1>
                      <h5
                        class="text-center"
                      >Enter your personal details and start journay with us</h5>
                    </v-card-text>
                    <div class="text-center">
                      <v-btn rounded outlined dark @click="step++">SIGN UP</v-btn>
                    </div>
                  </v-col>
                </v-row>
              </v-window-item>
              <v-window-item :value="2">
                <v-row class="fill-height">
                  <v-col cols="12" md="4" class="teal accent-3">
                    <v-card-text class="white--text mt-12">
                      <h1 class="text-center display-1">Welcome Back!</h1>
                      <h5
                        class="text-center"
                      >To Keep connected with us please login with your personnel info</h5>
                    </v-card-text>
                    <div class="text-center">
                      <v-btn rounded outlined dark @click="step--">Sign in</v-btn>
                    </div>
                  </v-col>

                  <v-col cols="12" md="8">
                    <v-card-text class="mt-12">
                      <h1 class="text-center display-2 teal--text text--accent-3">Create Account</h1>
                      <h4 class="text-center mt-4">Ensure your email for registration</h4>
                      <v-form>
                        <v-text-field
                          label="Name"
                          name="Name"
                          prepend-icon="mdi-account-circle"
                          type="text"
                          color="teal accent-3"
                        />
                        <v-text-field
                          label="Email"
                          name="Email"
                          prepend-icon="mdi-email"
                          type="text"
                          color="teal accent-3"
                        />
                        <v-text-field
                          id="password"
                          label="Password"
                          name="password"
                          prepend-icon="mdi-lock-check"
                          type="password"
                          color="teal accent-3"
                        />
                        <v-text-field
                          id="password_confirmation"
                          label="password again"
                          name="password_confirmation"
                          prepend-icon="mdi-lock-check"
                          type="password"
                          color="teal accent-3"
                        />
                      </v-form>
                    </v-card-text>
                    <div class="text-center mt-n5">
                      <v-btn rounded color="teal accent-3" dark>SIGN UP</v-btn>
                    </div>
                  </v-col>
                </v-row>
              </v-window-item>
            </v-window>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
</template>

<script>
export default {
  data: () => ({
    step: 1
  }),
  props: {
    source: String
  }
};
</script>



<style>
.errorMsg {
  color: red;
  margin-bottom: 0 !important;
}
</style>
