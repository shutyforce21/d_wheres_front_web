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
        <v-btn
          depressed
          color="#0277BD"
          to="/login"
        >Login</v-btn>
        <v-btn
          depressed
          color="#00BFA5"
          to="/register"
        >Register</v-btn>
      </div>

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
export default {
  name: 'DefaultLayout',
  data () {
    return {
      authenticated: false,
      clipped: false,
      drawer: false,
      fixed: false,
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
  beforeCreate() {
    axios.get("http://localhost/api/spots")
      .then((res) => {
        for (var mkr of res.data.data) {
          this.features.push({
            type: "Feature",
            properties: {
              message: "Bar",
              iconSize: [35, 35],
            },
            geometry: {
              type: "Point",
              coordinates: [
                Number(mkr.location.latitude),
                Number(mkr.location.longitude),
              ],
            },
          });
        }
        this.showSpots();
      })
      .catch((err) => {
        console.log(err);
      });
  },
  mounted() {
    if (localStorage.getItem("authentication")) {
      let authJson = JSON.parse(window.localStorage.getItem("authentication"));
      this.authenticated = true;
    }
  }
}
</script>
