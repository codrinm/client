<script setup>
import { ref } from 'vue';
import axios from 'axios';
import { onMounted } from '@vue/runtime-core';
import { toast } from 'vue3-toastify';

const data = ref([]);

onMounted(() => getData());

const getData = () => {
  axios
    .get('http://localhost:8000/api/dashboard')
    .then((res) => (data.value = res.data))
    .catch((err) => toast.error(err.message || 'An error occured!'));
};
</script>

<template>
  <main class="p-5">
    <h1 class="text-4xl font-semibold mb-10">Dashboard</h1>
    <div class="flex gap-5">
      <div
        class="w-72 flex justify-between items-center bg-white rounded-md shadow-md px-5 py-3"
      >
        <div class="text-lg font-semibold">Total Bookings</div>
        <div
          class="w-10 h-10 bg-indigo-600 text-white rounded-full text-lg font-semibold flex justify-center items-center"
        >
          {{ data.bookings_count }}
        </div>
      </div>

      <div
        class="w-72 flex justify-between items-center bg-white rounded-md shadow-md px-5 py-3"
      >
        <div class="text-lg font-semibold">Vehicle Makes</div>
        <div
          class="w-10 h-10 bg-indigo-600 text-white rounded-full text-lg font-semibold flex justify-center items-center"
        >
          {{ data.vehicle_makes_count }}
        </div>
      </div>

      <div
        class="w-72 flex justify-between items-center bg-white rounded-md shadow-md px-5 py-3"
      >
        <div class="text-lg font-semibold">Vehicle Models</div>
        <div
          class="w-10 h-10 bg-indigo-600 text-white rounded-full text-lg font-semibold flex justify-center items-center"
        >
          {{ data.vehicle_models_count }}
        </div>
      </div>
    </div>
  </main>
</template>
