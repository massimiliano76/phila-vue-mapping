<template>
  <div>
    <slot />
  </div>
</template>

<script>
  import { Popup, Point } from 'leaflet';
  export default {
    name: 'PopUp',
    mounted() {
      // console.log('popup mounted is running');
      const leafletElement = this.$leafletElement = this.createLeafletElement();
      // leafletElement.addTo(this._map);
      leafletElement.on('remove', this.removePopup)
    },
    destroyed() {
      // console.log('pop-up destroyed is running')
      this.$leafletElement.removeFrom(this._map);
    },
    watch: {
      intersectingFeatures(nextIntersectingFeatures) {
        // console.log('Popup WATCH intersectingFeatures is firing');
        const leafletElement = this.$leafletElement = this.createLeafletElement();
        // leafletElement.addTo(this._map);
        leafletElement.on('remove', this.removePopup)
      }
    },
    computed: {
      _map() {
        return this.$store.state.map.map;
      },
      intersectingFeatures() {
        return this.$store.state.map.intersectingFeatures;
      },
      popupCoords() {
        return this.$store.state.map.popupCoords;
      }
    },
    methods: {
      removePopup() {
        // console.log('closed Popup, this:', this);
        this.$store.commit('setIntersectingFeatures', []);
      },
      createLeafletElement() {
        const popup = new Popup({
          minWidth: 300,
          offset: new Point(0, -24)
        }).setLatLng(this.popupCoords)
          .setContent(this.$children[0].$el)
          .openOn(this._map);

        return popup;
      }
    }
  };
</script>
