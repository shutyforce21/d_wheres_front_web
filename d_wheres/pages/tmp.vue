<template>
  <v-card max-width="100%" class="mx-auto">
    <v-btn><v-icon>mdi-account</v-icon>user</v-btn>
    <v-btn><v-icon>mdi-map-marker</v-icon>spot</v-btn>
    <!-- <v-toolbar
      color="cyan"
      dark
    >
      <v-app-bar-nav-icon></v-app-bar-nav-icon>
       <v-toolbar-title>
       </v-toolbar-title>
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
    </v-toolbar> -->

    <!-- <v-container fluid>
      <v-row align="center">
        <v-col cols="12">
          <v-autocomplete
            v-model="values"
            :items="master.spot.prefectures"
            outlined
            dense
            chips
            small-chips
            label="Prefectures"
            multiple
          ></v-autocomplete>
          <v-autocomplete
            v-model="values"
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
            v-model="values"
            :items="master.spot.prefectures"
            outlined
            dense
            chips
            small-chips
            label="Prefectures"
            multiple
          ></v-autocomplete>
          <v-autocomplete
            v-model="values"
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
    </v-container> -->
    <template v-slot:progress>
      <v-progress-linear
        absolute
        color="green lighten-3"
        height="4"
        indeterminate
      ></v-progress-linear>
    </template>
    <v-img
      height="200"
      src="https://cdn.vuetifyjs.com/images/cards/dark-beach.jpg"
    >
      <v-row>
        <v-col
          class="text-right"
          cols="12"
        >
          <v-menu
            bottom
            left
            transition="slide-y-transition"
          >
            <template v-slot:activator="{ on, attrs }">
              <v-btn
                icon
                v-bind="attrs"
                v-on="on"
              >
                <v-icon>mdi-dots-vertical</v-icon>
              </v-btn>
            </template>
            <v-list>
              <v-list-item @click="isUpdating = true">
                <v-list-item-action>
                  <v-icon>mdi-cog</v-icon>
                </v-list-item-action>
                <v-list-item-content>
                  <v-list-item-title>Update</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list>
          </v-menu>
        </v-col>
        <v-row
          class="pa-4"
          align="center"
          justify="center"
        >
          <v-col class="text-center">
            <h3 class="text-h5">
              {{ name }}
            </h3>
            <span class="grey--text text--lighten-1">{{ title }}</span>
          </v-col>
        </v-row>
      </v-row>
    </v-img>
    <v-form>
      <v-container>
        <v-row>
          <v-col
            cols="12"
            md="6"
          >
            <v-text-field
              v-model="name"
              :disabled="isUpdating"
              filled
              color="blue-grey lighten-2"
              label="Name"
            ></v-text-field>
          </v-col>
          <v-col
            cols="12"
            md="6"
          >
            <v-text-field
              v-model="title"
              :disabled="isUpdating"
              filled
              color="blue-grey lighten-2"
              label="Title"
            ></v-text-field>
          </v-col>
          <v-col cols="12">
            <v-autocomplete
              v-model="friends"
              :disabled="isUpdating"
              :items="master.spot.prefectures"
              filled
              chips
              color="blue-grey lighten-2"
              label="Select"
              item-text="name"
              item-value="name"
              multiple
            >
            <v-autocomplete
              v-model="values"
              :items="master.spot.prefectures"
              outlined
              dense
              chips
              small-chips
              label="Prefectures"
              multiple
            ></v-autocomplete>
              <template v-slot:selection="data">
                <v-chip
                  v-bind="data.attrs"
                  :input-value="data.selected"
                  close
                  @click="data.select"
                  @click:close="remove(data.item)"
                >
                  <v-avatar left>
                    <v-img :src="data.item.avatar"></v-img>
                  </v-avatar>
                  {{ data.item.name }}
                </v-chip>
              </template>
              <template v-slot:item="data">
                <template v-if="typeof data.item !== 'object'">
                  <v-list-item-content v-text="data.item"></v-list-item-content>
                </template>
                <template v-else>
                  <v-list-item-avatar>
                    <img :src="data.item.avatar">
                  </v-list-item-avatar>
                  <v-list-item-content>
                    <v-list-item-title v-html="data.item.name"></v-list-item-title>
                    <v-list-item-subtitle v-html="data.item.group"></v-list-item-subtitle>
                  </v-list-item-content>
                </template>
              </template>
            </v-autocomplete>
          </v-col>
        </v-row>
      </v-container>
    </v-form>
    <v-divider></v-divider>
    <v-card-actions>
      <v-switch
        v-model="autoUpdate"
        :disabled="isUpdating"
        class="mt-0"
        color="green lighten-2"
        hide-details
        label="Auto Update"
      ></v-switch>
      <v-spacer></v-spacer>
      <v-btn
        :disabled="autoUpdate"
        :loading="isUpdating"
        color="blue-grey darken-3"
        depressed
        @click="isUpdating = true"
      >
        <v-icon left>
          mdi-magnify
        </v-icon>
        Search
      </v-btn>
    </v-card-actions>
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
  </v-card>
</template>

<script>
import axios from 'axios'

export default {
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
        },
        user: {
          name: '',
        }
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
      console.log(res.data);
      if (res.data.message == 'success') {
        var spots = res.data.data
        console.log(spots)
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

<style>

</style>
