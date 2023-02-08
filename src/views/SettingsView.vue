<script setup>
import axios from 'axios';
import { onMounted } from '@vue/runtime-core';
import { toast } from 'vue3-toastify';
import { reactive, ref } from 'vue';

const slots = ref([]);

const blockedDay = ref('');

onMounted(() => getSlots());

const getSlots = () => {
  axios
    .get('http://localhost:8000/api/slots')
    .then((res) => (slots.value = res.data))
    .catch((err) =>
      toast.error(err.message || 'An error occured, please try again!')
    );
};

const onSubmit = () => {
  axios
    .post('http://localhost:8000/api/slots', { slots: slots.value })
    .then((res) => toast.success('Slots have been updated!'))
    .catch((err) =>
      toast.error(err.message || 'An error occured, please try again!')
    );
};

const blockDay = () => {
  if (!blockedDay) {
    toast.error('Please select a day to block');
    return;
  }

  axios
    .post('http://localhost:8000/api/block-day', { date: blockedDay.value })
    .then((res) => {
      blockedDay.value = '';
      toast.success('Day has been blocked!');
    })
    .catch((err) =>
      toast.error(
        err.response?.data?.message || 'An error occured, please try again!'
      )
    );

  console.log(blockedDay.value);
};
</script>

<template>
  <main class="p-5">
    <div class="flex justify-between items-center mb-10">
      <h1 class="text-4xl font-semibold">Settings</h1>
    </div>

    <div class="flex gap-10">
      <form @submit.prevent="onSubmit">
        <div class="flex w-1/2 flex-col flex-wrap gap-2">
          <h2 class="px-5 py-4 text-xl font-semibold underline">
            Update Business Slots
          </h2>
          <div v-for="slot in slots" :key="slot.id">
            <div
              class="w-full flex items-center bg-white rounded-md shadow-md px-5 py-3"
            >
              <div class="text-lg font-semibold mr-4 w-32">{{ slot.day }}</div>
              <label for="from" class="mr-3">From</label>
              <input
                class="text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal h-10 items-center pl-3 text-sm border-gray-300 rounded border"
                type="time"
                id="from"
                v-model="slot.from"
              />

              <label for="to" class="mr-3 ml-8">To</label>
              <input
                class="text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal h-10 items-center pl-3 text-sm border-gray-300 rounded border"
                type="time"
                v-model="slot.to"
              />

              <label for="steps" class="mr-3 ml-20">Steps</label>
              <select
                class="text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal h-10 items-center pl-3 text-sm border-gray-300 rounded border"
                id="steps"
                v-model="slot.step"
              >
                <option value="30">30</option>
                <option value="60">60</option>
              </select>

              <label for="off" class="mr-3 ml-20">Off</label>
              <input
                class="text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal h-10 items-center pl-3 text-sm border-gray-300 rounded border"
                id="off"
                type="checkbox"
                v-model="slot.off"
              />
            </div>
          </div>
          <button
            type="submit"
            class="focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-700 transition duration-150 ease-in-out hover:bg-indigo-600 bg-indigo-700 rounded text-white px-8 py-2 text-sm"
          >
            Update slots
          </button>
        </div>
      </form>

      <div class="flex w-1/2 flex-col flex-wrap gap-2">
        <h2 class="px-5 py-4 text-xl font-semibold underline">Block Day</h2>
        <div class="flex items-center bg-white rounded-md shadow-md px-5 py-3">
          <div class="text-lg font-semibold mr-2 w-32">Day</div>
          <input
            class="text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal h-10 items-center pl-3 text-sm border-gray-300 rounded border"
            type="date"
            min="2023-02-08"
            max="2024-02-08"
            v-model="blockedDay"
          />
          <button
            class="ml-4 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-700 transition duration-150 ease-in-out hover:bg-indigo-600 bg-indigo-700 rounded text-white px-8 py-2 text-sm disabled:opacity-50 disabled:cursor-not-allowed"
            :disabled="!blockedDay"
            @click="blockDay"
          >
            Block
          </button>
          <p class="ml-auto text-sm italic">
            Block a particular day in the future.
          </p>
        </div>
      </div>
    </div>
  </main>
</template>
