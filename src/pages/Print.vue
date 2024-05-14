<template>
  <!--  -->
  <div class="h-screen hidden print:block text-gray-900">
    <!-- bg-white z-10 -->
    <div class="w-full h-full ">
      <div class="h-full p-8">
        <div class="h-[15%] flex items-center font-serif">
          <div class="w-[22%] flex items-center justify-center">
            <img src="../assets/ndrmc.png" alt="NDRMC Logo" width="200" />
          </div>
          <div class="w-[56%] text-sm flex flex-col items-center font-medium justify-center">
            <div class="leading-4">Republic of the Philippines</div>
            <div class="leading-4">N.D.R.R.M.C</div>
            <div class="leading-4">SWIFTAID</div>
            <div class="leading-4 font-bold">EMERGENCY DISPATCH CENTER</div>
            <div class="leading-4">Cebu City</div>
          </div>
          <div class="w-[22%] flex items-center justify-center">
            <img src="../assets/SWIFTAID_logo.png" alt="SWIFTAID Logo" width="100" />
          </div>
        </div>
        <div class="h-[40%] text-sm ">
          <div class="font-bold leading-10">INCIDENT REPORT</div>
          <div class="pl-8 font-medium">
            <div class="flex leading-10">
              <div class="w-[30%]">EMERGENCY TYPE</div>
              <div class="w-[20%] text-center">:</div>
              <div class="w-[50%]">{{ emergency }}</div>
            </div>
            <div class="flex leading-10">
              <div class="w-[30%]">RESPONDER</div>
              <div class="w-[20%] text-center">:</div>
              <div class="w-[50%]">{{ responder }}</div>
            </div>
            <div class="flex leading-10">
              <div class="w-[30%]">OPERATOR</div>
              <div class="w-[20%] text-center">:</div>
              <div class="w-[50%]">{{ operator }}</div>
            </div>
            <div class="flex leading-10">
              <div class="w-[30%]">DATE</div>
              <div class="w-[20%] text-center">:</div>
              <div class="w-[50%]">{{ date }}</div>
            </div>
            <div class="flex leading-10">
              <div class="w-[30%]">TIME POSTED</div>
              <div class="w-[20%] text-center">:</div>
              <div class="w-[50%]">{{ time }}</div>
            </div>
            <div class="flex leading-10">
              <div class="w-[30%]">TIME SENT</div>
              <div class="w-[20%] text-center">:</div>
              <div class="w-[50%]">{{ timeSent }}</div>
            </div>
            <div class="flex leading-10">
              <div class="w-[30%]">TIME RESPONDED</div>
              <div class="w-[20%] text-center">:</div>
              <div class="w-[50%]">{{ timeResponded }}</div>
            </div>
          </div>
          <br />
          <hr class="border border-gray-800" />
        </div>
        <div class="h-[30%] font-medium text-sm ">
          <div>
            <strong class="leading-10">POST DESCRIPTION :</strong>
            <p class="pl-8">{{ description }}</p>
          </div>
          <br />
          <div>
            <strong class="leading-10">ADDITIONAL DESCRIPTION :</strong>
            <p>{{ additionalDescription }}</p>
          </div>
          <br />
        </div>
        <div class="h-[10%] font-medium text-sm">
          <div class="text-end font-bold">{{ operator }}</div>
          <div class="leading-4 text-end">Dispatch Operator</div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
@media print {
  .print\:block {
    display: block !important;
  }
}
</style>

<script setup>
import { onMounted } from 'vue';

const emergency = localStorage.getItem('l_emergency') || '';
const responder = localStorage.getItem('l_responder') || '';
const operator = localStorage.getItem('l_operator') || '';
const date = localStorage.getItem('l_date') || '';
const time = localStorage.getItem('l_time') || '';
const timeSent = localStorage.getItem('l_timeSent') || '';
const timeResponded = localStorage.getItem('l_timeResponded') || '';
const description = localStorage.getItem('l_description') || '';
const additionalDescription = localStorage.getItem('l_additional_description') || '';

onMounted(() => {
  setTimeout(() => {
    window.print();
    window.close();
  }, 2000); // Delay for 2000 milliseconds
});
</script>
