<template>
  <div class="">
    <!-- Map container -->
    <div id="map" class="z-0 h-full border-2 border-gray-400 rounded-xl" style="height: 500px; width: 482px"></div>
    <div v-if="loading" class="mt-4">Loading...</div>

    <!-- Top 3 Nearest locations -->
    <div class="mt-2" v-if="topThreeOrigins.length != 0 && isVisible">
      
      <ul class="">
        <li
          v-for="(origin, index) in topThreeOrigins"
          :key="index"
          class="mb-2"
        >
          <span class="font-semibold">{{ origin.name.split(",")[0] }}</span
          >, Distance: {{ origin.distance.toFixed(2) }} km
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from "vue";
import L from "leaflet";
import "leaflet-routing-machine";
import stringSimilarity from "string-similarity";

const props = defineProps({
  inputText: String,
  type: String,
  isVisible: Boolean,
});

const responders = ref([]);
const detectedLocation = ref("");
const locations = ref([]);
const topThreeOrigins = ref([]);
const loading = ref(false); // Flag to indicate loading state

const getResponders = async () => {
  responders.value = [];
  const response = await fetch("http://localhost:8080/getResponderUnits");
  const data = await response.json();
  for (var i = 0; i < data.length; i++) {
    responders.value.push({
      name: data[i].name,
      location: data[i].location,
      type: data[i].type,
    });
  }
};

getResponders();

let mapInstance = null;

const calculateDistance = (lat1, lon1, lat2, lon2) => {
  const R = 6371;
  const dLat = ((lat2 - lat1) * Math.PI) / 180;
  const dLon = ((lon2 - lon1) * Math.PI) / 180;
  const a =
    Math.sin(dLat / 2) * Math.sin(dLat / 2) +
    Math.cos((lat1 * Math.PI) / 180) *
      Math.cos((lat2 * Math.PI) / 180) *
      Math.sin(dLon / 2) *
      Math.sin(dLon / 2);
  const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
  const distance = R * c;
  return distance;
};

const searchRoute = async () => {
  loading.value = true; // Set loading state to true

  detectLocation();
  var city = await location_city(detectedLocation.value);
  const destinationQuery = encodeURIComponent(
    `${detectedLocation.value} ${city}`
  );
  console.log(destinationQuery);

  // Filter out empty origin inputs
  console.log(city)
  
  const originQueries = responders.value
    .filter(
      (responder) =>
        responder.name.trim() !== "" &&
        responder.location === city &&
        responder.type == props.type
    )
    .map((responder) => encodeURIComponent(responder.name));

  try {
    const destinationResponse = await fetch(
      `https://nominatim.openstreetmap.org/search?q=${destinationQuery}&format=json`
    );
    const destinationData = await destinationResponse.json();

    if (destinationData.length > 0) {
      const { lat: destLat, lon: destLon } = destinationData[0];

      // Calculate distances for all origin points
      const originDistances = [];
      for (const originQuery of originQueries) {
        const originResponse = await fetch(
          `https://nominatim.openstreetmap.org/search?q=${originQuery}&format=json`
        );
        const originData = await originResponse.json();
        if (originData.length > 0) {
          const {
            lat: originLat,
            lon: originLon,
            display_name,
          } = originData[0];
          const distance = calculateDistance(
            originLat,
            originLon,
            destLat,
            destLon
          );
          originDistances.push({
            name: display_name,
            lat: originLat,
            lon: originLon,
            distance,
          });
        }
      }

      originDistances.sort((a, b) => a.distance - b.distance);

      topThreeOrigins.value = originDistances.slice(0, 3);

      for (const origin of topThreeOrigins.value) {
        showRoute(origin.lat, origin.lon, destLat, destLon);
      }
    }
  } catch (error) {
    console.error("Error searching locations:", error);
  } finally {
    loading.value = false; // Set loading state to false after search completes
  }
};

const showRoute = (originLat, originLon, destLat, destLon) => {
  if (mapInstance === null) {
    mapInstance = L.map("map").setView([originLat, originLon], 15);
    L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
      attribution:
        '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
    }).addTo(mapInstance);
  }

  const routingControl = L.Routing.control({
    waypoints: [L.latLng(originLat, originLon), L.latLng(destLat, destLon)],
    routeWhileDragging: true,
    show: false, // Hide routing instructions
  }).addTo(mapInstance);

  // Add markers for origin and destination points
  L.marker([originLat, originLon], { icon: redIcon }).addTo(mapInstance);
  L.marker([destLat, destLon]).addTo(mapInstance);

  // Hide routing instructions by modifying CSS
  const routeContainer = routingControl.getContainer();
  routeContainer.style.display = "none";
};

const redIcon = L.icon({
  iconUrl:
    "https://cdn.rawgit.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-red.png",
  shadowUrl:
    "https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.7.1/images/marker-shadow.png",
  iconSize: [25, 41],
  iconAnchor: [12, 41],
  popupAnchor: [1, -34],
  shadowSize: [41, 41],
});

const getLocations = async () => {
  const response = await fetch("http://localhost:8080/getLocation");
  const data = await response.json();
  for (var i = 0; i < data.length; i++) {
    locations.value.push(data[i].name);
  }
};
getLocations();

const detectLocation = () => {
  const sentence = props.inputText.toLowerCase().replace(/\s+/g, "");
  const matchedLocation = locations.value.find((location) =>
    sentence.includes(location)
  );

  if (matchedLocation) {
    detectedLocation.value = matchedLocation;
  } else {
    const similarities = locations.value.map((location) =>
      stringSimilarity.compareTwoStrings(sentence, location)
    );
    const maxSimilarityIndex = similarities.indexOf(Math.max(...similarities));
    detectedLocation.value =
      maxSimilarityIndex !== -1 ? locations.value[maxSimilarityIndex] : "";
  }
};

const location_city = async (detectedLocation) => {
  const response = await fetch("http://localhost:8080/getLocation");
  const data = await response.json();
  for (var i = 0; i < data.length; i++) {
    if (detectedLocation == data[i].name) {
      return data[i].location;
    }
  }
};

watch(
  [() => props.inputText, () => props.type],
  ([newInputText, newType], [oldInputText, oldType]) => {
    // Only trigger searchRoute if either inputText or type has changed
    if (newInputText !== oldInputText && newType !== oldType) {
      searchRoute();
    }
  }
);
</script>
