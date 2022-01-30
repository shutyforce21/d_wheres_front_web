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
      <v-toolbar-title v-text="title" />

      <div v-if="authenticated">
        <v-avatar color="indigo">
          <v-icon dark>
            mdi-account-circle
          </v-icon>
        </v-avatar>
      </div>

      <div v-else>
        <div class="bbbbb">
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
                <v-card
                  class="mx-10"
                >
                  <v-text-field
                    v-model="loginForm.email"
                    label="E-mail"
                  />
                  <v-text-field
                    v-model="loginForm.password"
                    type="password"
                    label="Password"
                  />
                  <v-btn @click="login('http://localhost/api/login')">login</v-btn>
                </v-card>
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
                <v-btn @click="register('http://localhost/api/register')">send</v-btn>
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
      </div>

    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>

    <!-- tabのMerginをなくす！ -->
    <v-footer
      padless=true
    >
      <v-tabs
       align-with-title
       grow
       >
        <v-tab>Tab 1</v-tab>
        <v-tab>Tab 2</v-tab>
        <v-tab>Tab 3</v-tab>
      </v-tabs>
    </v-footer>

    <!-- <v-footer
      :absolute="!fixed"
      app
    >
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer> -->
  </v-app>
</template>

<script>
import axios from "axios";
// import $cookies from "cookie-universal-nuxt";

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
      title: 'd_wheres',
      token: ''
    }
  },
  created() {
    const token = this.$cookies.get('token');
    if (token) {
      axios.get(
        "http://localhost/api/is_authenticated",
        { headers: { Authorization: "Bearer " + token } }
      )
        .then((res) => {
          if (res.data.message == 'success') {
            this.authenticated = true;
          }
        })
        .catch((err) => {
          console.log(err);
        });
    }
  },
  mounted() {},
  methods: {
    login: async function(endpoint) {
      try {
        // remove all cookies
        this.$cookies.removeAll();

        const res = await axios.post(endpoint, this.loginForm)
        if (res.data.message == 'success') {
          const { token, user_id } = res.data.data;

          this.$cookies.set('token', token);
          this.$cookies.set('user_id', user_id);
          //認証フラグをTrue
          this.authenticated = true;

          this.$router.push('/')

        } else {
          this.errMsgs = res.data.errors
        }

      } catch (error) {
          console.log('post Error');
          console.error(error);
      }
    },
    register: async function(endpoint) {
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

<style scoped>
.v-toolbar__title {
  flex-grow: 1;
}
.bbbbb {
    display: none;
  }
@media screen and (min-width: 769px) {
  .bbbbb {
    display: block;
  }
  .v-footer {
    padding: 0px;
  }
}
</style>
