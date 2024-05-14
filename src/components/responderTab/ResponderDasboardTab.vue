<template>
  <div class="flex h-full w-full">
    <div class="w-[100%] h-full flex space-x-5">
      <div class="w-[28%] h-full bg-white rounded-lg">
        <div
          class="text-white p-4 rounded-t-lg bg-primary font-bold text-2xl flex text-center justify-center items-center"
        >
          EMERGENCY REPORTS
        </div>

        <div class="px-4 pt-4 space-y-4 w-full">
          <testAlert
            v-for="(post, index) in posts"
            @click="getDetails(post.id)"
            :location="`${post.city} City`"
            :type="post.type"
            :time="post.time"
          />
        </div>
      </div>
      <div class="w-[72%] h-full">
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
                  {{ received }}
                </div>
                <div class="h-[35%] flex justify-center items-start text-sm">
                  Received
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
                  {{ dismissed }}
                </div>
                <div class="h-[35%] flex justify-center items-start text-sm">
                  Dismissed
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
        <div class="flex w-full h-[85%]">
          <div class="w-1/2 bg-white mr-3 h-full rounded-lg">
            <div
              class="text-white rounded-t-lg p-4 font-bold text-2xl flex text-center justify-center mx-auto bg-primary"
            >
              EMERGENCY DETAILS
            </div>
            <!-- v-if="postID" inside the div below -->
            <div v-if="postID"  class="h-[85%] w-full px-7 pt-3">
              <div class="h-full w-full">
                <div class="w-full h-full p-2 font-serif">
                  <div
                    class="bg-white h-full border drop-shadow-lg shadow-lg py-8 px-10"
                  >
                    <div class="h-[20%] flex">
                      <div
                        class="h-full w-[22%] flex items-center justify-center"
                      >
                        <img src="../../assets/ndrmc.png" alt="" />
                      </div>
                      <div
                        class="h-full w-[56%] text-[0.55rem] flex flex-col items-center font-medium justify-center gap-1"
                      >
                        <div class="leading-[0.58rem]">
                          Republic of the Philippines
                        </div>
                        <div class="leading-[0.58rem]">N.D.R.R.M.C</div>
                        <div class="leading-[0.58rem]">SWIFTAID</div>
                        <div class="leading-[0.58rem] font-bold">
                          EMERGENCY DISPATCH CENTER
                        </div>
                        <div class="leading-[0.58rem]">Cebu City</div>
                      </div>
                      <div
                        class="h-full w-[22%] flex justify-center items-center"
                      >
                        <img
                          src="../../assets/SWIFTAID_logo.png"
                          alt=""
                          width="48"
                        />
                      </div>
                    </div>
                    <div class="h-[30%] text-[0.60rem] mt-3">
                      <div class="font-bold ml-2">INCIDENT REPORT</div>
                      <div class="pl-8 font-medium space-y-1">
                        <div class="flex">
                          <div class="w-[30%]">EMERGENCY TYPE</div>
                          <div class="w-[20%] text-center">:</div>
                          <div class="w-[50%]">{{ emergency }}</div>
                        </div>
                        <div class="flex">
                          <div class="w-[30%]">RESPONDER</div>
                          <div class="w-[20%] text-center">:</div>
                          <div class="w-[50%]">
                            {{ responder }}
                          </div>
                        </div>
                        <div class="flex">
                          <div class="w-[30%]">OPERATOR</div>
                          <div class="w-[20%] text-center">:</div>
                          <div class="w-[50%]">{{ operator }}</div>
                        </div>
                        <div class="flex">
                          <div class="w-[30%]">DATE</div>
                          <div class="w-[20%] text-center">:</div>
                          <div class="w-[50%]">{{ date }}</div>
                        </div>
                        <div class="flex">
                          <div class="w-[30%]">TIME</div>
                          <div class="w-[20%] text-center">:</div>
                          <div class="w-[50%]">{{ time_ref }}</div>
                        </div>
                      </div>
                      <hr class="border border-gray-800 mt-3" />
                    </div>
                    <div
                      class="h-[40%] pt-5 pl-3 font-medium text-xs text-justify"
                    >
                      <div v-if="postDetails" class="leading-3 text-[0.68rem]">
                        <strong>Post:</strong> <br />{{ postDetails }}
                      </div>
                      <br />
                      <div
                        v-if="additionalDescription"
                        class="leading-3 text-[0.68rem]"
                      >
                        <strong>Additional Description:</strong> <br />{{
                          additionalDescription
                        }}
                      </div>
                      <br />
                      <div
                        v-if="eru.length != 0"
                        class="leading-3 text-[0.68rem]"
                      >
                        <strong>Deployable Units:</strong>
                        <p v-for="(unit, index) in eru">
                          {{ unit }}
                        </p>
                      </div>
                    </div>
                    <div class="h-[10%] font-medium text-[0.60rem]">
                      <div class="leading-[0.60rem] text-end font-bold">
                        {{ operator }}
                      </div>
                      <div class="leading-[0.60rem] text-end">
                        Dispatch Operator
                      </div>
                    </div>
                  </div>
                  <!-- End for paper -->
                </div>
              </div>
            </div>
          </div>
          <div class="w-1/2 bg-white h-full ml-2 rounded-lg">
            <div
              class="text-white bg-primary font-bold text-2xl flex text-center justify-center rounded-t-lg p-4 mx-auto"
            >
              EMERGENCY LOCATION
            </div>
            <div>
              <div class="h-[550px]">
                <div class="flex items-center justify-center h-full">
                  <!-- MAP -->
                  <Map
                    :inputText="postDetails"
                    :type="mapType"
                    :isVisible="false"
                  />
                  <!-- END MAP -->
                </div>

                <div
                  class="flex justify-center items-center h-[10%] px-5 gap-2"
                >
                  <button
                    @click="dismiss"
                    class="btn bg-white text-primary hover:bg-primary hover:text-white border-primary px-10 w-1/2"
                  >
                    Dismiss
                  </button>

                  <button
                    @click="acknowledgePost()"
                    class="btn bg-primary text-white hover:bg-white hover:text-primary hover:border-primary px-6 w-1/2"
                  >
                    Acknowledge
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <dialog ref="dialog" :id="id" class="modal">
    <div class="modal-box h-[300px] bg-white flex flex-col rounded-lg">
      <form method="dialog">
        <button class="btn btn-md btn-circle btn-ghost absolute right-2 top-2">
          ✕
        </button>
      </form>
      <div>
        <div class="relative p-4 w-[100%] mt-[3%]">
          <div class="items-center justify-center flex mb-2">
            <mdicon
              class="text-red-600"
              name="AlertOutline"
              :width="65"
              :height="65"
            />
          </div>
          <div
            class="text-center relative bg-white rounded-lg text-3xl font-bold text-red-600"
          >
            Emergency Alert
          </div>
          <p class="text-xl text-center tracking-wider mt-5 font-medium">
            Please see the emergency report immediately!
          </p>
        </div>
      </div>
    </div>
  </dialog>
</template>

<script setup>
import TestAlert from "../../testComposables/testAlert.vue";
import Map from "../../composables/Map.vue";
import axios from "axios";
import { ref, onMounted } from "vue";

onMounted(() => {
  initializeDataFetching();
});

const posts = ref([]);
const postID = ref();
const additionalDescription = ref("");

const city = ref();
const zipcode = ref();

const mapText = ref();

const eru = ref([]);
const date = ref();
const time_ref = ref();

const singlePost = async (id) => {
  const response = await fetch(`http://localhost:8080/getSinglePostTeam/${id}`);
  const data = await response.json();
  let responderName = await getResponder();
  for (var i = 0; i < data.length; i++) {
    if (data[i].name == responderName) {
      return data[i].name;
    }
  }
};
const singlePostTeam = async (id) => {
  const response = await fetch(``);
};
const responder = ref();

const getResponder = async () => {
  const response = await fetch("http://localhost:8080/getResponder");
  const data = await response.json();

  for (var i = 0; i < data.length; i++) {
    if (data[i].user_id == localStorage.getItem("responder_userId")) {
      responder.value = data[i].institution;
      return data[i].institution;
    }
  }
};


const received = ref(0);
const dismissed = ref(0);
const acknowledged = ref(0);
const pending = ref(0);

const getPost = async () => {
  try {
    posts.value = [];
    received.value = 0 ;
    dismissed.value = 0;
    acknowledged.value =0;
    pending.value = 0;
    const response = await fetch(`http://localhost:8080/getPost`);
    const data = await response.json();
    var lth = 0;
    for (var i = 0; i < data.length; i++) {
      if (data[i].status == "Sent") {
        const singlePostName = await singlePost(data[i].post_id);
        const responderName = await getResponder();

        if (singlePostName == responderName) {
          const timestamp = new Date(data[i].timestamp);
          const months = [
            "January",
            "February",
            "March",
            "April",
            "May",
            "June",
          ];

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
          date.value = `${months[month - 1]} ${day}, ${year}`;
          time_ref.value = time;
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
          lth++;
        }
        pending.value++;
        received.value++;

      }
      if (data[i].status == "Cancelled") {
        const singlePostName = await singlePost(data[i].post_id);
        const responderName = await getResponder();

        if (singlePostName == responderName) {
          dismissed.value++;
          received.value++;
        }
      }
      if (data[i].status == "Acknowledged") {
        const singlePostName = await singlePost(data[i].post_id);
        const responderName = await getResponder();

        if (singlePostName == responderName) {
          acknowledged.value++;
          received.value++;
        }
      }
    }

    return lth;
  } catch (error) {
    console.log(error);
  }
};

const postDetails = ref();
const location = ref();

const operator = ref();

const emergency = ref();
const mapType = ref();

const getDetails = async (id) => {
  postID.value = id;

  const response = await fetch(`http://localhost:8080/getSinglePost/${id}`);
  const data = await response.json();
  emergency.value = data[0].emergency_type;
  postDetails.value = data[0].description;
  location.value = `${data[0].address}`;

  if (emergency.value == "Fire") {
    mapType.value = "fire_station";
  } else if (emergency.value == "Flood") {
    mapType.value = "fire_station";
  } else if (emergency.value == "Assault") {
    mapType.value = "police";
  } else if (emergency.value == "Injuries") {
    mapType.value = "hospital";
  } else if (emergency.value == "Biohazard") {
    mapType.value = "hospital";
  } else if (emergency.value == "Others") {
    mapType.value = "police";
  }

  city.value = data[0].city;
  zipcode.value = data[0].zipcode;
  mapText.value = data[0].description;

  const post_report = await getSinglePostReport(id);
  additionalDescription.value = post_report.additional_description;

  eru.value = await getSinglePostTeam(id);
  operator.value = await getSingleOperator(post_report.operator_id);
  searchLocation();
};

const getSinglePostReport = async (id) => {
  const response = await fetch(
    `http://localhost:8080/getSinglePostReport/${id}`
  );
  const data = await response.json();
  return data[0];
};

const getSinglePostTeam = async (id) => {
  const response = await fetch(`http://localhost:8080/getSinglePostTeam/${id}`);
  const data = await response.json();
  let array = [];
  for (var i = 0; i < data.length; i++) {
    array.push(data[i].name);
  }
  return array;
};

const getSingleOperator = async (id) => {
  const response = await fetch(`http://localhost:8080/getSingleOperator/${id}`);
  const data = await response.json();

  return `${data[0].first_name} ${data[0].last_name}`;
};

const updateTimeSent = async () => {
  await axios.put(`http://localhost:8080/updatePostTimeAcknowledged/${postID.value}`, {
          timeAcknowledged: new Date().toISOString(), // current timestamp

        });
}

const acknowledgePost = async () => {
  const post_report = await getSinglePostReport(postID.value);
  var operator_id = post_report.operator_id;
  try {
    await axios.put(`http://localhost:8080/updatePost/${postID.value}`, {
      responder_id: localStorage.getItem("responder_userId"),
      operator_id: operator_id,
      timeAcknowledged: new Date().toISOString(), // current timestamp
      additional_description: additionalDescription.value,
      post_id: postID.value,
    });
    const currentDateTime = new Date();
    currentDateTime.setHours(currentDateTime.getHours() + 8); // Add 8 hours for UTC+8
    const utcPlus8Timestamp = currentDateTime.toISOString();

    await axios.put(`http://localhost:8080/updatePostTimeAcknowledged/${postID.value}`, {
        timeAcknowledged: utcPlus8Timestamp,
    });

    await axios.put(`http://localhost:8080/acknowledgePost/${postID.value}`);

    window.location.reload();
  } catch (error) {
    console.error("Error acknowledging post:", error);
  }
};

const dismiss = async () => {
  const post_report = await getSinglePostReport(postID.value);
  var operator_id = post_report.operator_id;

  await axios.put(`http://localhost:8080/updatePost/${postID.value}`, {
      responder_id: localStorage.getItem("responder_userId"),
      operator_id: operator_id,
      additional_description: additionalDescription.value,
      post_id: postID.value,
    });

  await axios.put(`http://localhost:8080/denyPost/${postID.value}`);
  window.location.reload();
};


const dialog = ref(null);

const initializeDataFetching = async () => {
  try {
    // Initial data fetch
await getPost();
    // Polling every second
    setInterval(async () => {
      try {
        // const response = await fetch(`http://localhost:8080/getPost`);
        // const data = await response.json();
        // let lth = 0;
        // for (var i = 0; i < data.length; i++) {
        //   if (data[i].status == "Pending") {
        //     lth++;
        //   }
        // }
        // if (lth > posts.value.length) {
        //   getPost();
        //   const audio = new Audio(`src/assets/alert.mp3`);
        //   audio.play();
        // }
        const response = await fetch(`http://localhost:8080/getPost`);
        const data = await response.json();
        let lth = 0;
        for(var i = 0; i < data.length;i++){
          if(data[i].status=='Sent'){
            const singlePostName = await singlePost(data[i].post_id);
            const responderName = await getResponder();

            if (singlePostName == responderName) {
             lth++;
            }
          }
        }
        if(lth > posts.value.length){
          const audio = new Audio(`src/assets/alert.mp3`);
          audio.play();
          dialog.value.showModal();  
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
