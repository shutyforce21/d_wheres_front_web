<template>
  <div>
    <div id="map" />
    <div v-if="this.toggle"></div>
  </div>
</template>

<script>
import axios from "axios";
import mapboxgl from "mapbox-gl";
export default {
  data() {
    return {
      popup: false,
      type: "FeatureCollection",
      toggle: false,
      data: {
        lat: "",
        lng: "",
      },
      name: "",
      del: "",
      latitude: "",
      longitude: "",
      posts: [],
      marker: {},
      features: [
        {
          type: "Feature",
          properties: {
            message: "Bar",
            iconSize: [35, 35],
            title: "Mapbox",
            description: "Washington, D.C.",
          },
          geometry: {
            type: "Point",
            coordinates: [-62.21582, -14.971891],
          },
        },
        {
          type: "Feature",
          properties: {
            message: "Bar",
            iconSize: [35, 35],
            title: "Mapbox",
            description: "Washington, D.C.",
          },
          geometry: {
            type: "Point",
            coordinates: [-61.21582, -13.971891],
          },
        },
      ],
    };
  },
  mounted() {
    mapboxgl.accessToken =
      "pk.eyJ1Ijoic2h1dHlmb3JjZSIsImEiOiJja3c3dG1ja3YxdHN6MnZtbjlobHdpYmU0In0.CUgXUng_QUDPiDEGKnRQQw";
    const map = new mapboxgl.Map({
      container: "map",
      style: "mapbox://styles/mapbox/streets-v11",
      center: [-65.017, -16.457],
      zoom: 5,
    });

    // this.getSpots();

    map.on("click", function (e) {
      console.log(e)
      marker && marker.remove();
      const marker = new mapboxgl.Marker()
      marker.setLngLat({ lng: e.point.x, lat: e.point.y });
      // marker.setLngLat({ lng: '-61.21582', lat: '-13.971891' });
      marker.addTo(map);
      // .setPopup(
      //     new mapboxgl.Popup({ offset: 25 }) // add popups
      //     .setHTML(
      //         //`<h3>${marker.properties.title}</h3><p>${marker.properties.description}</p>`
      //         `<h2>ここに練習場所を追加する</h2>`
      //     )
      // )
      console.log(marker)
      console.log(e.lngLat.lng);

      // // viewで書き直す！ 関数で書く
      // new mapboxgl.Popup({ closeOnClick: false })
      // .setLngLat({ lng: e.lngLat.lng, lat: e.lngLat.lat})
      // .setHTML(
      //    "<h2>ここに練習場所を追加する</h2>" +
      //    "<button id='js-show-popup' onclick='popupImage("+e.lngLat.lat+","+e.lngLat.lng+")'>Show Popup</button>"
      // )
      // .addTo(map);

      // this.map.flyTo({
      //     center: { lng: e.lngLat.lng, lat: e.lngLat.lat },
      //     zoom: 15
      // });

      // this.latitude = JSON.stringify(e.lngLat.lat)
      // this.longitude = JSON.stringify(e.lngLat.lng)
    });
  },
  methods: {
    getSpots: function () {
      axios
        .get("http://localhost/api/spots")
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
    showSpots: function () {
      console.log(this.map);
      for (const marker of this.features) {
        const el = document.createElement("img");
        const width = marker.properties.iconSize[0];
        const height = marker.properties.iconSize[1];
        el.className = "marker";
        el.src = "/img/map/marker.png";
        el.style.width = `${width}px`;
        el.style.height = `${height}px`;
        el.style.color = "red";
        //el.addEventListener('click', () => {
        //  window.alert(marker.properties.message);
        //});

        // Add markers to the map.
        new mapboxgl.Marker(el)
          .setLngLat(marker.geometry.coordinates)
          .setPopup(
            new mapboxgl.Popup({ offset: 25 }) // add popups
              .setHTML(
                //`<h3>${marker.properties.title}</h3><p>${marker.properties.description}</p>`
                `<h2>ここに練習場所を追加する</h2>`
              )
          )
          .addTo(this.map);

        //new mapboxgl.Popup({ closeOnClick: false })
        //.setLngLat(marker.geometry.coordinates)
        //.addTo(map);
      }
    },
  },
};
</script>

<style>
.mgl-map-wrapper {
  height: 700px;
  width: 500px;
}
</style>
