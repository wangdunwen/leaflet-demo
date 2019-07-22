<template>
    <div id='map'>
    </div>
</template>

<script>
export default {
  name: 'map',
  data () {
      return {
        
      }
  },
  mounted() { 
      let MapBoxUrl = 'https://api.tiles.mapbox.com/v4/{id}/{z}/{x}/{y}.png?' +
      'access_token=pk.eyJ1IjoibWFwYm94IiwiYSI6ImNpejY4NXVycTA2emYycXBndHRqcmZ3N3gifQ.rJcFIG214AriISLbB6B5aw';

      let OpenStreetMapUrl = 'http://{s}.tile.osm.org/{z}/{x}/{y}.png';
      let streetMap = L.tileLayer(OpenStreetMapUrl, {
            attribution: ''
      });

      let ArcGisMapUrl = 'https://services.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}';
      let ArcGisChineseUrl = 'http://[arcgisserver]/ArcGIS/rest/services/TileMAP/MapServer/tile/{z}/{y}/{x}';
      let arcGisMap = L.tileLayer(ArcGisMapUrl, {
            attribution: 'ArcGis'
      });

      window.map = L.map('map', {
          center: new L.LatLng(31.3111384, 120.6253701),
          layers: [streetMap],
          zoom: 10,
          zoomControl: false
      });

      //添加zoom控制
      let zoomControl = L.control.zoom({
          position: "bottomright"
      });
      map.addControl(zoomControl);

      //添加比例尺
      L.control.scale().addTo(map);
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    #map {
        position: absolute;
        width: 100%;
        height: 100%;
    }
</style>
