<template>
  <div>
    <section class="ui two column centered grid" style="position: relative; z-index: 1;">
      <div class="column">
        <form action="" class="ui segment large form">
          <div class="ui message red" v-show="error">
            {{ error }}
          </div>
          <div class="ui segment">
            <div class="field">
              <div class="ui right icon input large" :class="{loading:spinner}">
                <input 
                  type="text" 
                  placeholder="Enter your address" 
                  v-model="address" 
                  ref="autocomplete">
                <i class="dot circle link icon" @click="locatorButtonPressed"></i>
              </div>
            </div>
            <button class="ui button">Go</button>
          </div>
        </form>
      </div>
    </section>

    <section id="map"></section>
  </div>
</template>

<script>

import axios from 'axios';

export default {

  data() {
    return {
      address: "",
      error: "",
      spinner: false
    }
  },

  mounted() {
    var autocomplete = new google.maps.places.Autocomplete(
      this.$refs["autocomplete"],
      {
        bounds: new google.maps.LatLngBounds(
          new google.maps.LatLng(34.0531, -6.79846)
        )
      }
    );

    autocomplete.addListener("place_changed", () => {
      let place = autocomplete.getPlace();
      console.log(place);
      this.showUserLocationOnTheMap(place.geometry.location.lat(), place.geometry.location.lng());
    })
  },

  methods: {
    locatorButtonPressed() {
      this.spinner = true;
      if(navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(position => {
          this.getAddressFrom(
            position.coords.latitude, 
            position.coords.longitude
          );

          this.showUserLocationOnTheMap(
            position.coords.latitude, 
            position.coords.longitude
          );
        },
        error => {
          this.error = "Locator is unable to find the address. Please type your address manually";
          this.spinner = false;
          //console.log(error.message);
        }
        );
      } else {
        this.error = "Your browser does not support geolocation API";
        console.log("Your browser does not support geolocation API");
        this.spinner = false;
      }
    },
    getAddressFrom(lat, long) {
      axios.get("https://maps.googleapis.com/maps/api/geocode/json?latlng=" + lat + "," + long + "&key=YOUR_API_KEY")
      .then(response => {
        if(response.data.error_message) {
          this.error = response.data.error_message;
          console.log(response.data.error_message);
        } else {
          this.address = response.data.results[0].formatted_address;
          console.log(response.data.results[0].formatted_address);
        }
        this.spinner = false;
      })
      .catch(error => {
        this.error = error.message;
        console.log(error.message);
        this.spinner = false;
      })
    },
    showUserLocationOnTheMap(lat, long) {
      // Create a Map Object
      let map = new google.maps.Map(
        document.getElementById("map"), {
          zoom: 15,
          center: new google.maps.LatLng(lat, long),
          mapTypeId: google.maps.MapTypeId.ROADMAP
        }
        );

      // Add Marker
      new google.maps.Marker({
        position: new google.maps.LatLng(lat, long),
        map: map
      });
    }
  }
}
</script>


<style>
.ui.button, .dot.circle.icon {
  background-color: #ff5a5f;
  color: white;
}

.pac-icon {
  display: none;
}

.pac-item {
  padding: 10px;
  font-size: 16px;
  cursor: pointer;
}

.pac-item:hover {
  background-color: #ececec;
}

.pac-item-query {
  font-size: 16px;
}

#map {
  position: absolute;
  top: 0;
  right: 0;
  left: 0;
  bottom: 0;
  background: red;
}
</style>