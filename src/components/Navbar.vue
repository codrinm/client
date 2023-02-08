<script setup>
import { RouterLink } from 'vue-router';
</script>

<template>
  <nav class="flex justify-between bg-gray-900 text-white w-full">
    <div class="px-5 xl:px-12 py-4 flex w-full items-center">
      <RouterLink class="text-2xl font-bold font-heading" to="/">
        Hayden's Garage
      </RouterLink>
      <ul class="flex px-4 ml-auto font-semibold font-heading space-x-12">
        <li>
          <!-- <a class="hover:text-gray-200" href="#">Home</a> -->
          <RouterLink class="hover:text-red-500 duration-300" to="/"
            >Home</RouterLink
          >
        </li>
        <li>
          <RouterLink class="hover:text-red-500 duration-300" to="/dashboard"
            >Dashboard</RouterLink
          >
        </li>
        <li>
          <RouterLink class="hover:text-red-500 duration-300" to="/bookings"
            >Bookings</RouterLink
          >
        </li>
        <li>
          <RouterLink class="hover:text-red-500 duration-300" to="/settings"
            >Settings</RouterLink
          >
        </li>
      </ul>
    </div>
  </nav>
</template>
