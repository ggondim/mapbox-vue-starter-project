<template>
  <div class="vh-100 vw-100 d-flex">
    <div class="bg-light p-3" style="width: 400px;">
      <dl v-if="marker && marker.id" class="text-left">
        <dd class="d-inline-block mr-2">id</dd>
        <dt class="d-inline-block">{{ marker.id }}</dt>
        <span class="d-block" v-for="prop in properties" :key="prop">
          <dd class="d-inline-block mr-2">{{ prop }}</dd>
          <dt class="d-inline-block">{{ marker.properties[prop] }}</dt>
        </span>
      </dl>
    </div>
    <div id="map" class="w-100 h-100" />
  </div>
</template>

<script>
import mapboxgl from 'mapbox-gl/dist/mapbox-gl.js';

let map;

export default {
  data: () => ({
    marker: {},
  }),

  mounted() {
    map = new mapboxgl.Map({
      container: 'map',
      style: 'mapbox://styles/mapbox/streets-v11',
      center: [-55, -10],
      zoom: 4,
    });
    map.on('style.load', this.mapLoaded);
  },

  methods: {
    async mapLoaded() {
      map.addSource('points', {
        type: 'geojson',
        promoteId: 'id',
        data: 'http://localhost:7071/api/full',
      });
      map.addLayer({
        id: 'markers',
        type: 'circle',
        source: 'points',
        paint: {
          'circle-radius': ['interpolate', ['linear'], ['zoom'], 10, 2.5, 15, 10],
          'circle-color': '#f00',
        },
      });
      map.on('click', 'markers', this.markerClick);
    },

    markerClick(e) {
      this.marker = e.features[0];
    },
  },
}
</script>
