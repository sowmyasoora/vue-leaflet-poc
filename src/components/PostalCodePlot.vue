<template>
  <div>
    <form @submit.prevent="onSubmit">
      <input v-model="postalcode" placeholder="Enter postal code" />
      <div id="map"></div>
    </form>
  </div>
</template>

<script>
import "leaflet/dist/leaflet.css";
import L from "leaflet";


delete L.Icon.Default.prototype._getIconUrl;

L.Icon.Default.mergeOptions({
   iconRetinaUrl: require('leaflet/dist/images/marker-icon-2x.png'),
   iconUrl: require('leaflet/dist/images/marker-icon.png'),
   shadowUrl: require('leaflet/dist/images/marker-shadow.png'),
});

export default {
  name: "PostalCodePlot",
  data: () => {
    return {
      postalcode: "",
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
  methods: {
    onSubmit() {
      console.log("on submit");

      console.log(this.existingPostalCodes)

      this.existingPostalCodes.push(this.postalcode)

      let geolocationCoordinates = [];
    

      var map = L.map("map").setView([51.505, -0.09], 13);

      L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
        attribution:
          '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
      }).addTo(map);

        this.existingPostalCodes.forEach((postalcode) => {
            this.$http.get(`https://postcodes.io/postcodes/${postalcode}`).then(response => {

                // get body data
                const data  = response.body.result;
                console.log(data)

                this.geolocationCoordinates.push([data.latitude, data.longitude])

                //  L.marker([data.latitude, data.longitude]).addTo(map)
                //  .bindPopup(`${data.ccg}`)
                // .openPopup();

                }, response => {
                    // error callback
                    console.log(response)
            });
             
        })

        

       
    },
  },
};
</script>
<style scoped>
h1 {
  margin: 40px 0 0;
}
#map {
  height: 600px;
}
</style>