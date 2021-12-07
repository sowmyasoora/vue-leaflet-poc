<template>
  <div>
    <div class="card w-75 mx-auto">
      <div class="card-body">
        <div>
          <form @submit.prevent="onSubmit">
            <div class="form-group">
              <input
                type="text"
                class="form-control"
                id="postalcode"
                aria-describedby="postalcode"
                placeholder="Enter postal code"
                v-model="postalcode"
              />
            </div>
          </form>
          <div v:if="error">{{ error }}</div>
        </div>
      </div>
    </div>
    <div class="card map-container">
      <div class="card-body">
        <div id="map" class="mt-5"></div>
      </div>
    </div>
  </div>
</template>

<script>
import "leaflet/dist/leaflet.css";
import L from "leaflet";

delete L.Icon.Default.prototype._getIconUrl;

L.Icon.Default.mergeOptions({
  iconRetinaUrl: require("leaflet/dist/images/marker-icon-2x.png"),
  iconUrl: require("leaflet/dist/images/marker-icon.png"),
  shadowUrl: require("leaflet/dist/images/marker-shadow.png"),
});

export default {
  name: "PostalCodePlot",
  data: () => {
    return {
      postalcode: "",
      error: null,
      map: null,
      existingPostalCodes: [
        "EC2A 3AG",
        "N1 1RA",
        "W12 9BL",
        "W1U 6AA",
        "SW7 3HZ",
        "SW7 3DX",
        "W11 4UA",
        "SW13 9LB",
        "HP9 2JH",
        "HP9 1QD",
        "N1 2UQ",
        "W2 2HU",
        "W4 5DG",
        "NW5 2HR",
        "W11 2SE",
        "W8 6QD",
        "W11 3HL",
        "OL2 8NP",
        "W11 1LA",
        "NW3 2PT",
      ],
    };
  },
  mounted: function () {
    this.map = L.map("map").setView([51.505, -0.09], 10);
    L.tileLayer(
      "https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoibWFwYm94IiwiYSI6ImNpejY4NXVycTA2emYycXBndHRqcmZ3N3gifQ.rJcFIG214AriISLbB6B5aw",
      {
        maxZoom: 18,
        attribution:
          'Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, ' +
          'Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
        id: "mapbox/light-v9",
        tileSize: 512,
        zoomOffset: -1,
      }
    ).addTo(this.map);
  },
  methods: {
    onSubmit: async function onSubmit() {
      if (!this.postalcode) {
        this.error = "please enter postal code";
        return;
      }

      //TODO
      //  validate postalcode using apis
      // if success move to next
      // else show error message

      // check if postal code exists in the array,
      //if yes show the message

      if (this.existingPostalCodes.includes(this.postalcode)) {
        this.error =
          "Postal code already exists, please enter another postal code";
        return;
      }
      this.existingPostalCodes.push(this.postalcode);

      // used to map using geoGson api
      let features = [];

      this.existingPostalCodes.forEach(async (postalcode) => {
        // const response = await this.$http
        //   .get(`https://postcodes.io/postcodes/${postalcode}`)
        //   .toPromise();

        // TODO: may need to replace with vue $http
        const response = await fetch(
          `https://postcodes.io/postcodes/${postalcode}`
        );
        // get body data

        const { result } = await response.json();

        if (result) {
            // pointing using leaflet market : planning to replace using geoGson api
          L.marker([result.latitude, result.longitude])
            .addTo(this.map)
            .bindPopup(
              `${result.ccg} - ${result.admin_district}, ${result.admin_ward}, `
            )
            .openPopup();

          // used by map using geoGson api
          features.push({
            type: "Feature",
            properties: {
              popupContent: `${result.ccg} - ${result.admin_district}, ${result.admin_ward}, `,
            },
            geometry: {
              type: "Point",
              coordinates: [result.longitude, result.latitude],
            },
          });
        }
      });

      // used by  geoGson api : not working 
      const geoJson = {
        type: "FeatureCollection",
        features: features,
      };

      var geojsonMarkerOptions = {
        radius: 8,
        fillColor: "#ff7800",
        color: "#000",
        weight: 1,
        opacity: 1,
        fillOpacity: 0.8,
      };

      // neet to make it working
      L.geoJSON(geoJson, {
        pointToLayer: function (feature, latlng) {
          return L.circleMarker(latlng, geojsonMarkerOptions);
        },
      }).addTo(this.map);
    },
  },
};
</script>
<style scoped>
h1 {
  margin: 40px 0 0;
}
.card #map {
  height: 400px;
  width: 600px;
  margin-left: auto;
  margin-right: auto;
}
.card.map-container {
  border: none;
}
</style>