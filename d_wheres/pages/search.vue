<template>
  <v-card max-width="100%" class="mx-auto">
  <v-tabs>
    <v-tab href=#users><v-icon>mdi-account</v-icon>User</v-tab>
    <v-tab href=#spots><v-icon>mdi-map-marker</v-icon>Spot</v-tab>

    <v-tab-item value="users">
      <users />
    </v-tab-item>

    <v-tab-item value="spots">
      <spots />
    </v-tab-item>
    </v-tabs>

  </v-card>
</template>

<script>
import Users from '~/components/map/Users.vue';
import Spots from '~/components/map/Spots.vue';

export default {
  components: {
   Users,
   Spots
  },
  data() {
    return {
    }
  },
  mounted() {},
  methods: {}
}
</script>

<style>

</style>
