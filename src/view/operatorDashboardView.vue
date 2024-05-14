<template>
  <div class="flex h-full">
    <div
      class="bg-white h-full rounded-lg w-[28%] mr-3 p-5 overflow-auto space-y-5 shadow-lg"
    >
      <div class="font-semibold text-xl text-primary">Emergency Alert :</div>

      <!-- alert -->
      <testAlert
      :class="{ 'hidden': alertVisibility[index] }"
        v-for="(post, index) in posts.slice().reverse()"
        @click="getDetails(post.id),hideAlert(index)"
        :location="`${post.city}`+' City'"
        :type="post.type"
        :time="post.time"
        
      />  
    </div>
    <div class="h-full w-[72%] ml-2">
      <div class="flex h-[15%] pb-5 gap-5">
        <div class="bg-white w-1/4 h-full rounded-lg p-5 flex shadow-lg">
          <div class="h-full w-[40%]">
            <div  
              class="bg-primary h-full rounded-lg flex items-center justify-center text-white"
            >
              <mdicon
                class="origin-center"
                name="ProgressUpload"
                :width="50"
                :height="50"
              />
            </div>
          </div>
          <div class="h-full pl-3 w-[60%]">
            <div class="h-full">
              <div
                class="h-[65%] flex justify-center font-semibold items-end text-4xl"
              >
                {{ sent }}
              </div>
              <div class="h-[35%] flex justify-center items-start text-sm">
                Sent Reports
              </div>
            </div>
          </div>
        </div>
        <div class="bg-white w-1/4 h-full rounded-lg p-5 flex shadow-lg">
          <div class="h-full w-[40%]">
            <div
              class="bg-primary h-full rounded-lg flex items-center justify-center text-white"
            >
              <mdicon
                class="origin-center"
                name="ProgressClose"
                :width="50"
                :height="50"
              />
            </div>
          </div>
          <div class="h-full pl-3 w-[60%]">
            <div class="h-full">
              <div
                class="h-[65%] flex justify-center font-semibold items-end text-4xl"
              >
                {{ cancelled }}
              </div>
              <div class="h-[35%] flex justify-center items-start text-sm">
                Cancelled
              </div>
            </div>
          </div>
        </div>
        <div class="bg-white w-1/4 h-full rounded-lg p-5 flex shadow-lg">
          <div class="h-full w-[40%]">
            <div
              class="bg-primary h-full rounded-lg flex items-center justify-center text-white"
            >
              <mdicon
                class="origin-center"
                name="ProgressCheck"
                :width="50"
                :height="50"
              />
            </div>
          </div>
          <div class="h-full pl-3 w-[60%]">
            <div class="h-full">
              <div
                class="h-[65%] flex justify-center font-semibold items-end text-4xl"
              >
                {{ acknowledged }}
              </div>
              <div class="h-[35%] flex justify-center items-start text-sm">
                Acknowledged
              </div>
            </div>
          </div>
        </div>
        <div class="bg-white w-1/4 h-full rounded-lg p-5 flex shadow-lg">
          <div class="h-full w-[40%]">
            <div
              class="bg-primary h-full rounded-lg flex items-center justify-center text-white"
            >
              <mdicon
                class="origin-center"
                name="ProgressQuestion"
                :width="50"
                :height="50"
              />
            </div>
          </div>
          <div class="h-full pl-3 w-[60%]">
            <div class="h-full">
              <div
                class="h-[65%] flex justify-center font-semibold items-end text-4xl"
              >
                {{ pending }}
              </div>
              <div class="h-[35%] flex justify-center items-start text-sm">
                Pending Reports
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="h-[85%]">
        <div class="h-full flex">
          <div class="w-1/2 h-full bg-white rounded-lg shadow-lg mr-3">
            <EditReportPane
              @click="check"
              v-model:fire="fire"
              v-model:flood="flood"
              v-model:assault="assault"
              v-model:injuries="injuries"
              v-model:biohazard="biohazard"
              v-model:others="others"
              v-model:additionalDescription="additionalDescription"
              :city="city"
              :zipcode="zipcode"
              :postDetails="postDetails"
              :location="location"
              :emergencies="typeOfEmergency"
              :checked="checked"
              :emergency_team="city_responders"
              :deployed_team="deployed_team"
              :team_checked="team_checked"
            />
          </div>
          <div class="w-1/2  h-full p-5 bg-white rounded-lg shadow-lg ml-2">
            <div class="h-[95%]  w-full  ">
              <h2 class="text-lg text-start font-semibold text-primary mb-2">Map :</h2>
              <div
                class="flex flex-col items-center justify-center  h-full w-full overflow-hidden"
              >
              
                <!-- Setting overflow-hidden to prevent overflow -->
                
                <Map :inputText="mapText" :type="mapType" :isVisible="true" class="h-full "/>
              </div>
            </div>
            <div
              class="flex items-center h-[5%] gap-4 justify-center"
            >
            <button
                @click="denyPost()"
                class="w-[85%] h-12 bg-primary text-white rounded-lg font-semibold mb-2 hover:bg-white hover:text-primary hover:border-primary border-primary border-2 transition duration-300"
              >
                Discard
              </button>
              <button
                @click="sendPost()"
                class="w-[85%] h-12 bg-primary text-white rounded-lg font-semibold mb-2 hover:bg-white hover:text-primary hover:border-primary border-primary border-2 transition duration-300"
              >
                Send
              </button>
              
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script setup>
import testAlert from "../testComposables/testAlert.vue";
import EditReportPane from "../components/operatorPane/EditReportPane.vue";
import Map from "../composables/Map.vue";
import stringSimilarity from "string-similarity";
import { ref, onMounted, watch } from "vue";
import axios from "axios";

const alertVisibility = ref([]); // Array to track visibility of each alert

const hideAlert = (index) => {
  alertVisibility.value[index] = true; // Set visibility to true when alert is clicked
};


onMounted(() => {
  initializeDataFetching();
  getLocations();
  getResponders();
  alertVisibility.value = Array(posts.length).fill(false);
});

const posts = ref([]);
const postID = ref();
const additionalDescription = ref("");

const pending = ref(0);
const cancelled = ref(0);
const acknowledged = ref(0);
const sent = ref(0);
const locations = ref([]);
const responders = ref([]);
const city_responders = ref([]);

const city = ref();
const zipcode = ref();

const mapText = ref();
const mapType = ref();

const getPost = async () => {
  try {
    posts.value = [];
    sent.value = 0;
    pending.value = 0;
    acknowledged.value = 0;
    cancelled.value = 0;
    const response = await fetch(`http://localhost:8080/getPost`);
    const data = await response.json();

    for (var i = 0; i < data.length; i++) {
      if (data[i].status == "Pending") {
        const timestamp = new Date(data[i].timestamp);

        const month = timestamp.getMonth() + 1;
        const day = timestamp.getDate();
        const year = timestamp.getFullYear();

        let hours = timestamp.getHours();
        const minutes = timestamp.getMinutes();
        const ampm = hours >= 12 ? "PM" : "AM";
        hours = hours % 12;
        hours = hours ? hours : 12;
        const time =
          hours + ":" + (minutes < 10 ? "0" : "") + minutes + " " + ampm;

        posts.value.push({
          id: data[i].post_id,
          description: data[i].description,
          type: data[i].emergency_type,
          address: data[i].address,
          month: month,
          day: day,
          year: year,
          time: time,
          city: data[i].city,
          zipcode: data[i].zipcode,
        });
        pending.value++;
      } else if (data[i].status == "Sent") {
        sent.value++;
      } else if (data[i].status == "Acknowledged") {
        acknowledged.value++;
      } else if (data[i].status == "Cancelled") {
        cancelled.value++;
      }
    }
  } catch (error) {
    console.log(error);
  }
};

const postDetails = ref();
const location = ref();
const typeOfEmergency = ref([]);
const deployed_team = ref([]);

const fire = ref(false);
const flood = ref(false);
const assault = ref(false);
const injuries = ref(false);
const biohazard = ref(false);
const others = ref(false);

const getDetails = async (id) => {
  typeOfEmergency.value = [];
  city_responders.value = [];
  fire.value = false;
  flood.value = false;
  assault.value = false;
  injuries.value = false;
  biohazard.value = false;
  others.value = false;

  postID.value = id;

  const response = await fetch(`http://localhost:8080/getSinglePost/${id}`);
  const data = await response.json();
  postDetails.value = data[0].description;
  mapText.value = data[0].description;
  location.value = `${data[0].address}`;

  city.value = data[0].city;
  zipcode.value = data[0].zipcode;

  var description = data[0].description;
  var type = data[0].emergency_type;
  if (data[0].emergency_type == "Fire") {
    fire.value = true;
    getCityResponders(description, "fire");
    mapType.value = "fire_station";
  } else if (data[0].emergency_type == "Flood") {
    mapType.value = "fire_station";
    getCityResponders(description, "fire");
    flood.value = true;
  } else if (data[0].emergency_type == "Assault") {
    mapType.value = "police";
    getCityResponders(description, "police");

    assault.value = true;
  } else if (data[0].emergency_type == "Injuries") {
    getCityResponders(description, "hospital");
    
    mapType.value = "hospital";
    injuries.value = true;
  } else if (data[0].emergency_type == "Biohazard") {
    getCityResponders(description, "hospital");

    mapType.value = "hospital";
    biohazard.value = true;
  } else if (data[0].emergency_type == "Others") {
    getCityResponders(description, "police");
    mapType.value = "police";
    others.value = true;
  }
  typeOfEmergency.value.push({ emergency: data[0].emergency_type });
};

const getCityResponders = async (description, type) => {
  const location_name = detectLocation(description);
  const city = await location_city(location_name);

  for (var i = 0; i < responders.value.length; i++) {
    if (
      responders.value[i].location == city &&
      responders.value[i].type == "fire_station" &&
      type == "fire"
    ) {
      city_responders.value.push({ eru: responders.value[i].name });
    } else if (
      responders.value[i].location == city &&
      responders.value[i].type == "police" &&
      type == "police"
    ) {
      city_responders.value.push({ eru: responders.value[i].name });
    } else if (
      responders.value[i].location == city &&
      responders.value[i].type == "hospital" &&
      type == "hospital"
    ) {
      city_responders.value.push({ eru: responders.value[i].name });
    }
  }
};

function checked(isChecked, value) {
  console.log(isChecked, value);
  if (isChecked) {
    typeOfEmergency.value.push({ emergency: value });
  } else {
    const index = typeOfEmergency.value.findIndex(
      (item) => item.emergency === value
    );
    if (index !== -1) {
      typeOfEmergency.value.splice(index, 1);
    }
  }
}

function team_checked(isChecked, value) {
  console.log(isChecked, value);
  if (isChecked) {
    deployed_team.value.push({ eru: value });
  } else {
    const index = deployed_team.value.findIndex((item) => item.eru === value);
    if (index !== -1) {
      deployed_team.value.splice(index, 1);
    }
  }
}

// send poster
const editPostLocation = async (id) => {
  try {
    const response = await axios.put(
      `http://localhost:8080/updateLocation/${id}`,
      {
        address: location.value,
      }
    );
    // window.location.reload();
  } catch (error) {
    console.error(
      "Error acknowledging post:",
      error.message || "Unknown error"
    );
  }
};

const additionalResponseTeam = async (name) => {
  try {
    const formData = new FormData();
    formData.append("post_id", postID.value);
    formData.append("name", name);

    const response = await axios.post(
      "http://localhost:8080/addPostTeam",
      formData,
      {
        headers: {
          "Content-Type": "multipart/form-data",
        },
      }
    );
  } catch (error) {
    console.log("Error: ", error);
  }
};

const updateTimeSent = async () => {
  await axios.put(`http://localhost:8080/updatePostTimeSent/${postID.value}`, {
    timeSent: new Date().toISOString(), // current timestamp
  });
}

const sendPost = async () => {
  try {
    const operatorUserID = localStorage.getItem("operator_userId");
    const response = await fetch("http://localhost:8080/getOperator");
    const data = await response.json();
    for (var i = 0; i < data.length; i++) {
      if (operatorUserID == data[i].user_id) {
        const operatorID = data[i].operator_id;
        await axios.put(`http://localhost:8080/updatePost/${postID.value}`, {
          responder_id: null,
          operator_id: operatorID,
          additional_description: additionalDescription.value,
          post_id: postID.value,
        });
        const currentDateTime = new Date();
        currentDateTime.setHours(currentDateTime.getHours() + 8); // Add 8 hours for UTC+8
        const utcPlus8Timestamp = currentDateTime.toISOString();

        await axios.put(`http://localhost:8080/updatePostTimeSent/${postID.value}`, {
            timeSent: utcPlus8Timestamp,
        });        
        sentPost(postID.value);
        editPostLocation(postID.value);
        for (var i = 0; i < deployed_team.value.length; i++) {
          additionalResponseTeam(deployed_team.value[i].eru);
        }
        window.location.reload();
        break;
      }
    }
  } catch (error) {
    console.error("Error acknowledging post:", error);
  }
};

const sentPost = async (id) => {
  try {
    const response = await axios.put(`http://localhost:8080/sendPost/${id}`);
    // window.location.reload();
  } catch (error) {
    console.error(
      "Error acknowledging post:",
      error.message || "Unknown error"
    );
  }
};

const denyPost = async () => {
  try {
    const response = await axios.put(`http://localhost:8080/denyPost/${postID.value}`);
    const operatorUserID = localStorage.getItem("operator_userId");

    await axios.put(`http://localhost:8080/updatePost/${postID.value}`, {
          responder_id: null,
          operator_id: operatorUserID,
          additional_description: additionalDescription.value,
          post_id: postID.value,
        });
    window.location.reload();
  } catch (error) {
    console.error(
      "Error acknowledging post:",
      error.message || "Unknown error"
    );
  }
};

// location getter

const getLocations = async () => {
  const response = await fetch("http://localhost:8080/getLocation");
  const data = await response.json();
  for (var i = 0; i < data.length; i++) {
    locations.value.push(data[i].name);
  }
};

const getResponders = async () => {
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

const detectLocation = (text) => {
  const sentence = text.toLowerCase().replace(/\s+/g, "");
  const matchedLocation = locations.value.find((location) =>
    sentence.includes(location)
  );

  if (matchedLocation) {
    return matchedLocation;
  } else {
    const similarities = locations.value.map((location) =>
      stringSimilarity.compareTwoStrings(sentence, location)
    );
    const maxSimilarityIndex = similarities.indexOf(Math.max(...similarities));
    return maxSimilarityIndex !== -1 ? locations.value[maxSimilarityIndex] : "";
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

const location_zipcode = async (detectedLocation) => {
  const response = await fetch("http://localhost:8080/getLocation");
  const data = await response.json();
  for (var i = 0; i < data.length; i++) {
    if (detectedLocation == data[i].name) {
      return data[i].zipcode;
    }
  }
};

const initializeDataFetching = async () => {
  try {
    // Initial data fetch
    await getPost();

    // Polling every second
    setInterval(async () => {
      try {
        const response = await fetch(`http://localhost:8080/getPost`);
        const data = await response.json();
        let lth = 0;
        for (var i = 0; i < data.length; i++) {
          if (data[i].status == "Pending") {
            lth++;
          }
        }
        if (lth > posts.value.length) {
          getPost();

        }
      } catch (error) {
        console.error("Error fetching posts:", error);
      }
    }, 1000);
  } catch (error) {
    console.error("Error initializing data fetching:", error);
  }
};
</script>
