<template>
  <div class="leaflet-bar easy-button-container leaflet-control">
    <button @click="handleLocationButtonClick">
      <span class="button-state state-unnamed-state unnamed-state-active">
        <font-awesome-icon :icon="this.computedIcon" class="fa-lg"></font-awesome-icon>
      </span>
    </button>
  </div>
</template>

<script>
  import Control from '../leaflet/Control.vue';
  const {props, methods} = Control;

  export default {
    name: 'LocationControl',
    props: [
      'position'
    ],
    data() {
      return {
        locationOn: false
      }
    },
    computed: {
      computedIcon() {
        if (this.$config.geolocation) {
          if (this.$config.geolocation.icon) {
            return this.$config.geolocation.icon;
          } else {
            return 'dot-circle';
          }
        } else {
          return 'dot-circle';
        }
      }
    },
    methods: Object.assign(methods, {

      handleLocationButtonClick(e) {
        // document.getElementById('addressSearch').blur()
        // alert('handleLocationButtonClick is running');
        const watchPositionOn = this.$store.state.map.watchPositionOn;
        // console.log('watchPositionOn', watchPositionOn);
        if (!watchPositionOn) {
          this.$store.commit('setWatchPositionOn', true);
          navigator.geolocation.watchPosition(this.geofindSuccess, this.geofindError, {enableHighAccuracy: true, timeout: 1000, maximumAge: 0, distanceFilter: 5});
        } else {
          this.moveToPosition();
        }
      },
      geofindSuccess(position) {
        // alert('geofindSuccess is running, position:', position);
        const payload = {
          lat: position.coords.latitude,
          lng: position.coords.longitude
        }
        this.$store.commit('setLocation', payload);
        // console.log('latitude', payload.lat, 'longitude', payload.lng);
        if (!this.locationOn) {
          this.moveToPosition();
          this.locationOn = true;
        }
      },
      moveToPosition() {
        const map = this.$store.state.map.map;
        const location = this.$store.state.map.location;
        // console.log('LocationControl.vue moveToPosition is running, location:', location);
        map.setView([location.lat, location.lng], 19);
      },
      geofindError() {
        console.log('GeofindError')
      }
    })
  };
</script>

<style scoped>

</style>
