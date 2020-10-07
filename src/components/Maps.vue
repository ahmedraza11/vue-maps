<template>
  <div
    style="display: flex; justify-content:center; align-items:center;flex-direction:column;"
  >
    <div>
      <h1>Your Coordinates</h1>
      <p>{{ myCoordinates.lat }} Latitude, {{ myCoordinates.lng }} Longitude</p>
    </div>
    <div>
      <h1>Map Coordinates</h1>
      <p>
        {{ mapCoordinates.lat }} Latitude, {{ mapCoordinates.lng }} Longitude
      </p>
    </div>
    <GmapMap
      :center="myCoordinates"
      :zoom="zoom"
      style="width: 940px; height: 660px;"
      ref="mapRef"
      @dragend="handleDrag"
    >
    </GmapMap>
  </div>
</template>

<script>
export default {
  data() {
    return {
      map: null,
      zoom: 7,
      myCoordinates: {
        lat: 0,
        lng: 0,
      },
    };
  },
  created() {
    if (localStorage.center) {
      this.myCoordinates = JSON.parse(localStorage.center);
    } else {
      this.$getLocation({})
        .then((coord) => {
          this.myCoordinates = coord;
        })
        .catch((err) => alert(err));
    }

    if (localStorage.zoom) {
      this.zoom = parseInt(localStorage.zoom);
    }
  },
  mounted() {
    this.$refs.mapRef.$mapPromise.then((map) => (this.map = map));
  },
  computed: {
    mapCoordinates() {
      if (!this.map) {
        return {
          lat: 0,
          lng: 0,
        };
      }
      return {
        lat: this.map
          .getCenter()
          .lat()
          .toFixed(4),
        lng: this.map
          .getCenter()
          .lng()
          .toFixed(4),
      };
    },
  },
  methods: {
    handleDrag() {
      let center = {
        lat: this.map.getCenter().lat(),
        lng: this.map.getCenter().lng(),
      };
      let zoom = this.map.getZoom();
      localStorage.center = JSON.stringify(center);
      localStorage.zoom = zoom;
    },
  },
};
</script>
