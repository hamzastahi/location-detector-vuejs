# Location Detector with Vue.js and Geolocation API

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev
```

## Before using the project

Before starting to use the project, it's mandatory to use your API Keys that you could generate by following those instructions : https://developers.google.com/maps/documentation/javascript/get-api-key


After generating the key, change the following files on the specific lines as below :

> index.html : line 7
``` html
<script src="https://maps.googleapis.com/maps/api/js?libraries=places&key=YOUR_API_KEY"></script>
```

> src/pages/UserLocation.vue : line 89
``` javascript
axios.get("https://maps.googleapis.com/maps/api/geocode/json?latlng=" + lat + "," + long + "&key=YOUR_API_KEY")
```