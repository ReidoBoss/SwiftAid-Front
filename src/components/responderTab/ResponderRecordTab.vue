  <template>
  <div class="flex  h-full w-full  ">
    <div class="bg-white rounded-lg mt-2 shadow-lg w-full p-5">
      <div class="overflow-auto h-full">
        <table
          class="table table-zebra border border-gray-500 table-pin-rows table-pin-cols"
        >
          <!-- head -->
          <thead class="text-primary">
            <tr>
              <th></th>
              <th class="border border-gray-500">Location</th>
              <th class="border border-gray-500">Type of Emergency</th>
              <th class="border border-gray-500">Description</th>
              <th class="border border-gray-500">Status</th>
              <th class="border border-gray-500">Timestamp</th>
              <th class="border border-gray-500">Action</th>
            </tr>
          </thead>
          <tbody class="">
            <!-- row 1 -->
            <tr class="hover" v-for="(post,index) in posts">
              <th class="border border-gray-500">{{index+1}}</th>
              <td class="border border-gray-500">{{post.location}}</td>
              <td class="border border-gray-500">
                {{ post.emergency }}
              </td>
              <td class="border border-gray-300">{{post.description}}</td>
              <td class="border border-gray-300">{{post.status}}</td>
              <td class="border border-gray-300">{{post.timestamp}}</td>
              <td class="border border-gray-300">
                <button 
                class="text-start item-start justify-start flex"
                @click="print(post.emergency,responder,post.operator,post.timestamp,post.time,post.description,post.added_description,post.timeSent,post.timeResponded)">
                <dialogAction 
                  :emergency="post.emergency"
                  :date="post.timestamp"
                  :time="post.time"
                  :description="post.description"
                  :additionalDescription="post.added_description"
                  :operator="post.operator"
                  :responder="responder"
                />
              </button>
              </td>
            </tr>

          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script setup>
import dialogAction from "../../composables/dialogAction.vue";

import {ref,onMounted} from "vue";

onMounted(()=>{
  getPost();
  getResponder()
});

const posts = ref([
  {
    location: "123 Main St",
    emergency: "Fire",
    description: "Building on fire",
    status: "Acknowledged",
    timestamp: "May 9, 2024",
    time: "12:00 PM"
  },
  {
    location: "456 Elm St",
    emergency: "Flood",
    description: "Street flooded",
    status: "Acknowledged",
    timestamp: "May 8, 2024",
    time: "12:00 PM"
  },
  {
    location: "789 Oak St",
    emergency: "Assault",
    description: "Physical altercation",
    status: "Dismissed",
    timestamp: "May 7, 2024",
    time: "12:00 PM"
  },
  {
    location: "101 Pine St",
    emergency: "Injuries",
    description: "Multiple injuries reported",
    status: "Acknowledged",
    timestamp: "May 6, 2024",
    time: "12:00 PM"
  },
  {
    location: "111 Maple St",
    emergency: "Biohazard",
    description: "Chemical spill",
    status: "Dismissed",
    timestamp: "May 5, 2024",
    time: "12:00 PM"
  }
]);

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

const print = (emergency,responder,operator,date,time,description,additional_description,sent,responded) =>{
  localStorage.setItem('l_emergency',emergency);
  localStorage.setItem('l_responder',responder);
  localStorage.setItem('l_operator',operator);
  localStorage.setItem('l_date',date);
  localStorage.setItem('l_time',time);
  localStorage.setItem('l_timeSent',sent);
  localStorage.setItem('l_timeResponded',responded);

  localStorage.setItem('l_description',description);
  localStorage.setItem('l_additional_description',additional_description);
}




const getSinglePostReport = async (id) => {
  const response = await fetch(`http://localhost:8080/getSinglePostReport/${id}`);
  const data = await response.json();
  return data[0];
}

const getOperator = async (id) => {
  const response = await fetch(`http://localhost:8080/getSingleOperator/${id}`);
  const data = await response.json()
  console.log(id)
  return `${data[0].first_name} ${data[0].last_name}`;
}


const getPost = async () => {
  const response = await fetch('http://localhost:8080/getPost');
  const data = await response.json();

  for (var i = 0; i < data.length; i++) {
    const timestamp = new Date(data[i].timestamp);
    const months = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'];
    const monthIndex = timestamp.getMonth();
    const monthName = months[monthIndex];
    const day = timestamp.getDate();
    const year = timestamp.getFullYear();
    let hours = timestamp.getHours();
    const minutes = timestamp.getMinutes();
    const ampm = hours >= 12 ? 'PM' : 'AM';
    hours = hours % 12;
    hours = hours ? hours : 12;
    const time = hours + ':' + (minutes < 10 ? '0' : '') + minutes + ' ' + ampm;
    const singlePost = await getSinglePostReport(data[i].post_id);

    const timeSentStamp = new Date(singlePost.timeSent);
    const timeSentResponded = new Date(singlePost.timeAcknowledged);

    const formatTime = (date) => {
      let hours = date.getHours();
      const minutes = date.getMinutes();
      const ampm = hours >= 12 ? 'PM' : 'AM';
      hours = hours % 12;
      hours = hours ? hours : 12;
      return hours + ':' + (minutes < 10 ? '0' : '') + minutes + ' ' + ampm;
    };

    const formatDate = (date) => {
      const months = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'];
      const monthIndex = date.getMonth();
      const monthName = months[monthIndex];
      const day = date.getDate();
      const year = date.getFullYear();
      return `${monthName} ${day}, ${year}`;
    };

    if (singlePost.operator_id) {
      const added_description = singlePost.additional_description;
      const operator = await getOperator(singlePost.operator_id);
      let currentResponderID = localStorage.getItem("responder_userId");
      let status = null;
      if (currentResponderID == singlePost.responder_id && data[i].status == 'Acknowledged' || currentResponderID == singlePost.responder_id && data[i].status == 'Cancelled') {
        status = data[i].status;
        console.log("I'm true");
        if (data[i].status == 'Cancelled') {
          status = 'Dismissed';
        }
        posts.value.push({
          id: data[i].post_id,
          location: data[i].address,
          emergency: data[i].emergency_type,
          description: data[i].description,
          operator: operator,
          added_description: added_description,
          status: status,
          timestamp: `${monthName} ${day}, ${year}`,
          time: time,
          timeSent: `${formatTime(timeSentStamp)} ${formatDate(timeSentStamp)}`, // complete this
          timeResponded: `${formatTime(timeSentResponded)} ${formatDate(timeSentResponded)}`, // complete this
        });
      }
    }
  }
};

</script>
