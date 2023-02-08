<script setup>
import { ref } from '@vue/reactivity';
import axios from 'axios';
import { computed, onMounted } from '@vue/runtime-core';
import { toast } from 'vue3-toastify';
import { RouterLink } from 'vue-router';

const bookings = ref([]);
const filterDate = ref('');

onMounted(() => getBookings());

const getBookings = (params = {}) => {
  axios
    .get('http://localhost:8000/api/bookings', { params })
    .then((res) => (bookings.value = res.data))
    .catch((err) =>
      toast.error(err.message || 'An error occured, please try again!')
    );
};

const filterBookings = () => {
  const params = {
    date: filterDate.value,
  };
  getBookings(params);
};
</script>

<template>
  <main class="p-5">
    <div class="flex justify-between items-center mb-10">
      <h1 class="text-4xl font-semibold">Current Bookings</h1>
      <RouterLink
        class="cursor-pointer bg-green-700 text-white p-3 rounded-md font-medium hover:bg-green-600"
        to="/create-booking"
      >
        Create Booking
      </RouterLink>
    </div>

    <div class="py-4">
      <label class="mr-2">Filter by date: </label>
      <input
        class="mb-5 mt-2 text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal h-10 items-center pl-3 text-sm border-gray-300 rounded border"
        type="date"
        min="2023-02-08"
        max="2024-02-08"
        v-model="filterDate"
        @change="filterBookings()"
      />
      <button
        type="button"
        class="ml-4 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-700 transition duration-150 ease-in-out hover:bg-indigo-600 bg-indigo-700 rounded text-white px-8 py-2 text-sm"
        v-if="filterDate"
        @click="
          filterDate = '';
          getBookings();
        "
      >
        Reset
      </button>
    </div>

    <div v-if="bookings.length" class="flex flex-wrap gap-5">
      <div v-for="booking in bookings" :key="booking.id">
        <div class="w-72 bg-white rounded-md shadow-md px-5 py-3">
          <div class="text-lg font-semibold">{{ booking.name }}</div>
          <div class="italic">{{ booking.email }}</div>
          <div class="font-medium text-blue-500">
            {{ booking.phone || '-' }}
          </div>
          <div class="py-2"><hr /></div>
          <div class="text-lg font-semibold flex text-green-600">
            {{ booking.date }}
            <span class="ml-auto">@ {{ booking.time }}</span>
          </div>
        </div>
      </div>
    </div>
    <div v-else>No bookings found</div>
  </main>
</template>
