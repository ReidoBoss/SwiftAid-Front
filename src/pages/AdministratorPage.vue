<template>
  <div class="flex bg-secondary h-screen w-screen" v-if="auth">
    <div class="w-[20%] bg-primary h-screen">
      <div class="h-[25%] w-full flex items-center justify-center">
        <img class="w-80" src="../assets/SWIFTAID_+.png" alt="" />
      </div>
      <div
        class="h-[75%] w-full flex flex-col items-end text-2xl space-y-2 text-white font-medium"
      >
        <div class="flex items-center mx-auto flex-col w-[100%] gap-2 py-5">
          PROFILE MANAGEMENT
          <div
            :class="
              currentTab === 0 ? 'active-tab ml-10' : 'general-tab ml-10 '
            "
            @click="currentTab = 0"
          >
            <div class="flex gap-2 cursor-pointer">
              <div>
                <mdicon class="" name="FaceAgent" :width="65" :height="65" />
              </div>
              <div class="flex items-center">Operator</div>
            </div>
          </div>
          <div
            :class="
              currentTab === 1 ? 'active-tab ml-10' : 'general-tab ml-10 '
            "
            @click="currentTab = 1"
          >
            <div class="flex gap-2 cursor-pointer">
              <div>
                <mdicon
                  class=""
                  name="PoliceBadgeOutline"
                  :width="65"
                  :height="65"
                />
              </div>
              <div class="flex items-center">Responder</div>
            </div>
          </div>

          <div
            :class="currentTab === 2 ? 'active-tab ml-10' : 'general-tab ml-10'"
            @click="currentTab = 2"
          >
            <div class="flex gap-2 cursor-pointer">
              <div>
                <mdicon
                  class=""
                  name="FileAccountOutline"
                  :width="65"
                  :height="65"
                />
              </div>
              <div class="flex items-center">Profile Control</div>
            </div>
          </div>
        </div>

        <div
          :class="currentTab === 3 ? 'active-tab' : 'general-tab'"
          @click="
            showModal();
            isClicked = true;
          "
        >
          <div class="flex gap-2 cursor-pointer">
            <div>
              <mdicon class="" name="logout-variant" :width="65" :height="65" />
            </div>
            <div class="flex items-center">Sign Out</div>
          </div>
        </div>
      </div>
    </div>
    <div class="w-[80%]">
      <dialog
        v-if="isClicked == true"
        id="my_modal_3"
        class="modal"
        ref="myModal"
      >
        <div class="modal-box">
          <form method="dialog">
            <button
              class="btn btn-md btn-circle btn-ghost absolute right-2 top-2"
            >
              ✕
            </button>
          </form>
          <h1 class="font-bold text-xl">Sign Out</h1>
          <p class="py-4 text-center text-lg font-medium">
            Are you sure you want to sign out?
          </p>
          <div class="flex items-center justify-center">
            <button
              class="btn bg-primary text-white hover:bg-white hover:text-primary hover:border-primary"
              @click="logout"
            >
              Sign Out
            </button>
          </div>
        </div>
      </dialog>
      <div class="h-[13%] w-full p-5">
        <div class="flex w-full h-full bg-white rounded-lg shadow-xl">
          <div class="w-[77%] p-3">
            <div
              class="w-[100%] h-full rounded-xl text-primary font-semibold text-4xl flex items-center justify-start"
            >
              Welcome Back, {{ adminFirstName }}
            </div>
          </div>
          <div class="flex w-[23%]">
            <div class="w-[25%] flex items-center justify-end">
              <mdicon
                class="text-primary"
                name="account-circle"
                :width="70"
                :height="70"
              />
            </div>
            <div class="w-[75%]">
              <div
                class="pt-5 h-[50%] text-xl font-medium flex items-center justify-center text-primary"
              >
                <div>Administrator</div>
              </div>
              <div
                class="pb-5 h-[50%] text-l flex items-center justify-center text-primary"
              >
                Admin
              </div>
            </div>
          </div>
        </div>
      </div>
      <!-- Current tab -->
      <Transition name="fade" mode="out-in">
        <AdminAddOperator v-if="currentTab === 0" />
        <AdminAddResponder v-else-if="currentTab === 1" />
        <AdminManageAmbot v-else-if="currentTab === 2" />
        <!-- <AdminManageUser v-else-if="currentTab === 2" /> -->
        <!-- <AdminSignOutTab v-else-if="currentTab === 2" /> -->
      </Transition>
    </div>
  </div>
  <div v-else class="flex justify-center items-center h-48">
    <div
      class="animate-spin rounded-full h-16 w-16 border-t-4 border-b-4 border-purple-500"
    ></div>
    <div class="ml-4 text-purple-500">Loading...</div>
  </div>
</template>

<script setup>
import AdminAddOperator from "../components/administratorTab/AdminAddOperator.vue";
import AdminAddResponder from "../components/administratorTab/AdminAddResponder.vue";
// import AdminManageUser from "../components/administratorTab/AdminManageUser.vue";
import AdminManageAmbot from "../components/administratorTab/AdminManageAmbot.vue";
// import AdminSignOutTab from "../components/administratorTab/AdminSignOutTab.vue";

import { ref } from "vue";
import { onMounted } from "vue";
import { initFlowbite } from "flowbite";
import { useRouter } from "vue-router";

const currentTab = ref(0);
const router = useRouter();

const auth = ref();
auth.value = localStorage.getItem("adminAuth");

onMounted(() => {
  adminAuth();
  initFlowbite();
});

const adminFirstName = ref();
const adminLastName = ref();

const adminAuth = async () => {
  const admin = localStorage.getItem("adminAuth");
  if (!admin) {
    router.push("/");
  }

  const response = await fetch(`http://localhost:8080/getAdmin`);
  const data = await response.json();

  for (var i = 0; i < data.length; i++) {
    if (localStorage.getItem("admin_userId") == data[i].user_id) {
      adminFirstName.value = data[i].first_name;
      adminLastName.value = data[i].last_name;

      break;
    }
  }
};
const isClicked = ref(false); //clicked

const myModal = ref(null); //modal

const showModal = () => {
  if (myModal.value) {
    myModal.value.showModal();
  }
};

const logout = () => {
  localStorage.clear();
  router.push("/");
};

onMounted(() => {
  showModal(); // Optionally show the modal on component mount
});
</script>
