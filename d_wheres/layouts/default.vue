<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
    >
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="item.title" />
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar
      :clipped-left="clipped"
      fixed
      app
    >
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-btn
        icon
        @click.stop="miniVariant = !miniVariant"
      >
        <v-icon>mdi-{{ `chevron-${miniVariant ? 'right' : 'left'}` }}</v-icon>
      </v-btn>
      <v-btn
        icon
        @click.stop="clipped = !clipped"
      >
        <v-icon>mdi-application</v-icon>
      </v-btn>
      <v-btn
        icon
        @click.stop="fixed = !fixed"
      >
        <v-icon>mdi-minus</v-icon>
      </v-btn>
      <v-toolbar-title v-text="title" />
      <v-spacer />

      <div v-if="authenticated">

        <v-btn
          depressed
          color="red"
          to="/profile"
        >Profile</v-btn>
      </div>
      <div v-else>

          <v-dialog
            transition="dialog-bottom-transition"
            max-width="600"
          >
            <template v-slot:activator="{ on, attrs }">
              <v-btn
                color="primary"
                v-bind="attrs"
                v-on="on"
              >Login</v-btn>
            </template>
            <template v-slot:default="dialog">
              <v-card>
                <v-toolbar
                  color="primary"
                  dark
                >Login your account</v-toolbar>
                <v-text-field
                  v-model="loginForm.email"
                  label="E-mail"
                />
                <v-text-field
                  v-model="loginForm.password"
                  type="password"
                  label="Password"
                />
                <v-btn @click="sendLogin('http://localhost/api/login')">login</v-btn>
                <v-card-actions class="justify-end">
                  <v-btn
                    text
                    @click="dialog.value = false"
                  >Close</v-btn>
                </v-card-actions>
              </v-card>
            </template>
          </v-dialog>

          <v-dialog
            transition="dialog-top-transition"
            max-width="600"
          >
            <template v-slot:activator="{ on, attrs }">
              <v-btn
                color="success"
                v-bind="attrs"
                v-on="on"
              >Register</v-btn>
            </template>
            <template v-slot:default="dialog">
              <v-card>
                <v-toolbar
                  color="primary"
                  dark
                >Register your account!!</v-toolbar>
                <!-- <v-card-text>
                  <div class="text-h2 pa-12">Hello world!</div>
                </v-card-text> -->
                <v-text-field
                  v-model="registerForm.name"
                  label="Name"
                />
                <v-text-field
                  v-model="registerForm.email"
                  label="E-mail"
                />
                <v-text-field
                  v-model="registerForm.password"
                  type="password"
                  label="Password"
                />
                <v-text-field
                  v-model="registerForm.password_confirmation"
                  type="password"
                  label="Password_Confirmation"
                />
                <v-btn @click="sendRegister('http://localhost/api/register')">send</v-btn>
                <!-- <div v-show="this.errMsgs" class="errorMsgBox">
                <p class="errorMsg" v-for="(msg, index) in this.errMsgs" :key="index">
                  {{ msg.message && msg.message }}
                </p>
                </div> -->
                <v-card-actions class="justify-end">
                  <v-btn
                    text
                    @click="dialog.value = false"
                  >Close</v-btn>
                </v-card-actions>
              </v-card>
            </template>
          </v-dialog>

      </div>

      <v-row justify="space-around">
  </v-row>

    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-navigation-drawer
      v-model="rightDrawer"
      :right="right"
      temporary
      fixed
    >
      <v-list>
        <v-list-item @click.native="right = !right">
          <v-list-item-action>
            <v-icon light>
              mdi-repeat
            </v-icon>
          </v-list-item-action>
          <v-list-item-title>Switch drawer (click me)</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-footer
      :absolute="!fixed"
      app
    >
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
import axios from "axios";
export default {
  name: 'DefaultLayout',
  data () {
    return {
      authenticated: false,
      clipped: false,
      drawer: false,
      fixed: false,
      registerForm: {
        name: '',
        email: '',
        password: '',
        password_confirmation: ''
      },
      loginForm: {
        email: '',
        password: ''
      },
      items: [
        {
          icon: 'mdi-apps',
          title: 'Welcome',
          to: '/'
        },
        {
          icon: 'mdi-chart-bubble',
          title: 'Inspire',
          to: '/inspire'
        },
        {
          icon: 'mdi-chart-bubble',
          title: 'Map',
          to: '/map'
        },
        {
          icon: 'mdi-chart-bubble',
          title: 'Search',
          to: '/search'
        },
        {
          icon: 'mdi-chart-bubble',
          title: 'Profile',
          to: '/profile'
        },
        {
          icon: 'mdi-chart-bubble',
          title: 'Register',
          to: '/register'
        },
        {
          icon: 'mdi-chart-bubble',
          title: 'Login',
          to: '/login'
        }
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Vuetify.js'
    }
  },
  mounted() {
    if (localStorage.getItem("authentication")) {
      let authJson = JSON.parse(localStorage.getItem("authentication"));
      axios.get(
        "http://localhost/api/user/is_authenticated",
        { headers: { Authorization: "Bearer " + authJson.auth_token } }
      )
        .then((res) => {
          if (res.data.message == 'success') {
            this.authenticated = true;
          }
        })
        .catch((err) => {
          console.log(2);
          console.log(err);
        });
    }
  },
  methods: {
    sendLogin: async function(endpoint) {
      console.log(1)
      try {
        const res = await axios.post(endpoint, this.loginForm)
        console.log(res)
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

          console.log(1)

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
    },
    sendRegister: async function(endpoint) {
      try {
        const res = await axios.post(endpoint, this.registerForm)
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
