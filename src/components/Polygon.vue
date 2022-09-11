<template>
  <div>
    <div class="np-margins">
      <h4>
        Optimize Google Maps Polygon Using Ramer Douglas Peucker Algorithm
      </h4>
      <input type="number" v-model="kinkValue" placeholder="Kink value" />
      <button @click="optimizePolygon">Optimize polygon</button>
      <button @click="resetPolygon">Reset optimization</button>
      <p>Total coordinates: {{ this.drawablePolygonCoords.length }}</p>
    </div>
    <div id="polygon-label-map" class="map-margins"></div>
    <div class="np-credits">www.nightprogrammer.com</div>
  </div>
</template>

<script>
import loadGoogleMapsApi from "load-google-maps-api";
import { gMapsApiKey } from "./../constants";
import { RDouglasPeucker } from "./douglas-peucker";
import { polygonCoordinates } from "./polygon-coordinates";

export default {
  name: "PolygonMap",
  data() {
    return {
      map: null,
      googleInstance: null,
      kinkValue: 10,
      polygonCoords: polygonCoordinates,
      drawablePolygonCoords: [],
      polygonShape: null,
    };
  },
  methods: {
    optimizePolygon() {
      this.polygonShape.setMap(null);

      this.drawablePolygonCoords = RDouglasPeucker(
        this.polygonCoords,
        this.kinkValue
      );
      this.setPolygonOnMap(this.googleInstance, this.drawablePolygonCoords);
    },
    resetPolygon() {
      this.polygonShape.setMap(null);

      this.drawablePolygonCoords = this.polygonCoords;
      this.setPolygonOnMap(this.googleInstance, this.drawablePolygonCoords);
    },
    setPolygonOnMap(google, polygonCoordinates) {
      const tempBounds = new google.maps.LatLngBounds();

      for (let j = 0; j < polygonCoordinates.length; j++) {
        const x = {
          lat: polygonCoordinates[j].lat,
          lng: polygonCoordinates[j].lng,
        };
        const BoundLatLng = new google.maps.LatLng({
          lat: parseFloat(x.lat),
          lng: parseFloat(x.lng),
        });
        tempBounds.extend(BoundLatLng);
      }
      const centroid = tempBounds.getCenter();

      this.polygonShape = new google.maps.Polygon({
        paths: polygonCoordinates,
        strokeColor: "#ffffff",
        map: this.map,
        strokeWeight: 2,
        fillColor: "#7b00ff",
        fillOpacity: 0.6,
        zIndex: 99999,
        draggable: true,
      });

      this.polygonShape.setMap(this.map);

      this.map.setCenter(centroid);
      this.map.setZoom(16);
    },
  },
  mounted() {
    loadGoogleMapsApi({
      key: gMapsApiKey,
      libraries: ["places", "drawing", "geometry"],
    }).then(async () => {
      const mapZoom = 12;
      const { google } = window;
      const mapOptions = {
        zoom: mapZoom,
        mapTypeId: google.maps.MapTypeId.HYBRID,
        center: new google.maps.LatLng({ lat: 23, lng: 57 }),
        mapTypeControl: true,
        streetViewControl: false,
        mapTypeControlOptions: {
          position: google.maps.ControlPosition.BOTTOM_LEFT,
        },
      };
      this.map = new google.maps.Map(
        document.getElementById("polygon-label-map"),
        mapOptions
      );
      this.googleInstance = google;
      this.drawablePolygonCoords = this.polygonCoords;
      this.setPolygonOnMap(this.googleInstance, this.polygonCoords);
    });
  },
};
</script>

<style scoped>
.header-margins {
  margin-left: 30px;
  margin-top: 20px;
}
.np-margins {
  margin: 20px 30px;
}
.map-margins {
  height: 300px;
  width: 500px;
  margin: 20px 30px;
}
.np-credits {
  margin-left: 30px;
  margin-top: 4px;
  font-size: 12px;
}
button {
  background: rgb(0, 89, 255);
  color: #fff;
  border: 1px solid #fff;
  padding: 4px 10px;
  border-radius: 4px;
}
</style>