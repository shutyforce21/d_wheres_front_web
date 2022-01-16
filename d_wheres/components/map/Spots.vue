<template>
  <div>
    <v-toolbar
      color="cyan"
      dark
    >
      <v-app-bar-nav-icon></v-app-bar-nav-icon>
        <v-text-field
          label="Search Spot Name"
          v-model="searchParams.spot.name"
        ></v-text-field>
      <v-spacer></v-spacer>
      <v-btn icon
        @click="searchSpot('http://localhost/api/spots')"
      >
        <v-icon>mdi-magnify</v-icon>
      </v-btn>
      <v-btn icon>
        <v-icon>mdi-view-module</v-icon>
      </v-btn>
    </v-toolbar>

    <v-container fluid>
      <v-row align="center">
          <v-col cols="12">
          <v-autocomplete
            v-model="searchParams.prefectures"
            :items="master.spot.prefectures"
            outlined
            dense
            chips
            small-chips
            label="Prefectures"
            multiple
          ></v-autocomplete>
          <v-autocomplete
            v-model="searchParams.prefectures"
            :items="master.spot.prefectures"
            outlined
            dense
            chips
            small-chips
            label="Prefectures"
            multiple
          ></v-autocomplete>
        </v-col>
        <v-col cols="6">
          <v-autocomplete
            v-model="searchParams.prefectures"
            :items="master.spot.prefectures"
            outlined
            dense
            chips
            small-chips
            label="Prefectures"
            multiple
          ></v-autocomplete>
          <v-autocomplete
            v-model="searchParams.prefectures"
            :items="master.spot.prefectures"
            outlined
            dense
            chips
            small-chips
            label="Prefectures"
            multiple
          ></v-autocomplete>
        </v-col>
      </v-row>
    </v-container>

    <v-list three-line>
      <template v-for="(item, index) in items">
        <v-subheader
          v-if="item.header"
          :key="item.header"
          v-text="item.header"
        ></v-subheader>

        <v-divider
          v-else-if="item.divider"
          :key="index"
          :inset="item.inset"
        ></v-divider>

        <v-list-item
          v-else
          :key="item.title"
        >
          <v-list-item-avatar>
            <v-img :src="item.avatar"></v-img>
          </v-list-item-avatar>

          <v-list-item-content @click='showSpot(item.id)'>
            <v-list-item-title v-html="item.title"></v-list-item-title>
            <v-list-item-subtitle v-html="item.subtitle"></v-list-item-subtitle>
          </v-list-item-content>
        </v-list-item>
      </template>
    </v-list>
  </div>
</template>
<script>
import axios from 'axios'
import Users from '~/components/map/Users.vue'

export default {
  components: {
   Users
  },
  data() {
    return {
      master: {
        spot: {
          prefectures: [
            '東京都',
            '神奈川県',
            '埼玉県',
            '東京都1',
            '神奈川県1',
            '埼玉県1',
            '東京都2',
            '神奈川県2',
            '埼玉県2',
            '東京都3',
            '神奈川県3',
            '埼玉県3'
          ]
        },
        user: {},
      },
      searchParams: {
        spot: {
          name: '',
          prefectures:[]
        },
      },
      items: [
        { header: 'Today' },
        {
          avatar: 'https://cdn.vuetifyjs.com/images/lists/1.jpg',
          title: 'Brunch this weekend?',
          subtitle: `<span class="text--primary">Ali Connors</span> &mdash; I'll be in your neighborhood doing errands this weekend. Do you want to hang out?`,
        },
        { divider: true, inset: true },
        {
          avatar: 'https://cdn.vuetifyjs.com/images/lists/2.jpg',
          title: 'Summer BBQ <span class="grey--text text--lighten-1">4</span>',
          subtitle: `<span class="text--primary">to Alex, Scott, Jennifer</span> &mdash; Wish I could come, but I'm out of town this weekend.`,
        },
        { divider: true, inset: true },
        {
          avatar: 'https://cdn.vuetifyjs.com/images/lists/3.jpg',
          title: 'Oui oui',
          subtitle: '<span class="text--primary">Sandra Adams</span> &mdash; Do you have Paris recommendations? Have you ever been?',
        },
        { divider: true, inset: true },
        {
          avatar: 'https://cdn.vuetifyjs.com/images/lists/4.jpg',
          title: 'Birthday gift',
          subtitle: '<span class="text--primary">Trevor Hansen</span> &mdash; Have any ideas about what we should get Heidi for her birthday?',
        },
        { divider: true, inset: true },
        {
          avatar: 'https://cdn.vuetifyjs.com/images/lists/5.jpg',
          title: 'Recipe to try',
          subtitle: '<span class="text--primary">Britta Holt</span> &mdash; We should eat this: Grate, Squash, Corn, and tomatillo Tacos.',
        },
      ]
    }
  },
  mounted() {
    axios.get("http://localhost/api/spots")
    .then((res) => {
      console.log(res);
      if (res.data.message == 'success') {
        var spots = res.data.data
        for (var spot of spots) {
          this.items.push({divider: true, inset: true})
          this.items.push(
            {
              id: spot.id,
              avatar: spot.image,
              title: spot.name,
              subtitle: spot.content
            }
          )
        }
      } else {
        this.errMsgs = res.data.errors
      }
    })
    .catch((err) => {

    })
  },
  methods: {
    searchSpot() {
      var spotParams = this.searchParams.spot;

      axios.get("http://localhost/api/spots", {
        params: {
          name: spotParams.name
        }
      })
      .then((res) => {

        if (res.data.message == 'success') {
          const spots = res.data.data

          for (let spot of spots) {
            this.items.push({divider: true, inset: true})
            this.items.push(
              {
                id: spot.id,
                avatar: spot.image,
                title: spot.name,
                subtitle: spot.content
              }
            )
          }
        } else {
          this.errMsgs = res.data.errors
        }
      })
      .catch((err) => {

      })
    },
    showSpot(id) {
      this.$router.push({name: 'spot', query: { spotId: id }})
    }
  }
}
</script>
