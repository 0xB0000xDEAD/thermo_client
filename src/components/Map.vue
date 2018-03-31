<template>
<div class="map-container">
  <gmap-map :center="{lat:10, lng:10}" :zoom="7" map-type-id="terrain" style="width: 500px; height: 300px"></gmap-map>

</div>
</template>

<script>
import * as VueGoogleMaps from 'vue2-google-maps';
Vue.use(VueGoogleMaps, {
  load: {
    key: 'AIzaSyBJoqpzXmAlOuZ2e-eKP4wDe9YfLpnWhiY',
    libraries: 'places' // This is required if you use the Autocomplete plugin
    // OR: libraries: 'places,drawing'
    // OR: libraries: 'places,drawing,visualization'
    // (as you require)
  }
});
export default {
  data: function() {
    return {
      //data placeholder
    };
  }
};
</script>

<style>
</style>
