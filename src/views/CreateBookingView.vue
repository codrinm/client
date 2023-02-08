<script setup>
import { reactive, ref, onMounted } from 'vue';
import axios from 'axios';
import { toast } from 'vue3-toastify';
import { RouterLink } from 'vue-router';
import CreateBookingModal from '../components/CreateBookingModal.vue';

const slots = ref([]);
const showBookingModal = ref(false);

const modalRef = reactive({
  show: false,
  date: null,
  time: null,
});

const toggleModal = (show, date, time) => {
  modalRef.date = date;
  modalRef.time = time;
  modalRef.show = show;
};

const created = (booking) => {
  toggleModal(false, null, null);

  const foundSlot = slots.value.find((slot) => slot.date === booking.date);

  if (foundSlot) {
    const filteredTimeSlots = foundSlot.time_slots.filter(
      (tslot) => tslot !== booking.time
    );
    foundSlot.time_slots = filteredTimeSlots;
  }
};

const getBookings = () => {
  axios
    .get('http://localhost:8000/api/available-slots')
    .then((res) => (slots.value = res.data))
    .catch((err) =>
      toast.error(err.message || 'An error occured, please try again!')
    );
};

onMounted(() => getBookings());
</script>

<template>
  <main class="p-5">
    <div class="flex justify-between items-center mb-10">
      <h1 class="text-4xl font-semibold">Create Booking</h1>
      <RouterLink
        class="cursor-pointer p-3 text-blue-500 font-medium hover:text-blue-600"
        to="/bookings"
      >
        &lt; back to Bookings
      </RouterLink>
    </div>

    <div class="flex flex-col gap-5">
      <div v-for="slot in slots" :key="slot.id">
        <div
          class="w-full bg-white rounded-md shadow-md px-5 py-3"
          :class="{ 'bg-red-200': slot.off }"
        >
          <div class="text-lg font-semibold mb-4">
            {{ slot.day }}, {{ slot.nice_date }}
          </div>

          <div v-if="!slot.off">
            <div
              v-if="slot.time_slots.length"
              class="flex flex-wrap mb-1 gap-2"
            >
              <div v-for="(time, index) in slot.time_slots" :key="index">
                <span
                  class="bg-green-400 font-semibold p-2 rounded-md cursor-pointer hover:bg-green-500"
                  @click="toggleModal(true, slot.date, time)"
                  >{{ time }}</span
                >
              </div>
            </div>
            <div v-else>
              <div class="text-xl font-semibold italic text-red-500">
                FULLY BOOKED
              </div>
            </div>
          </div>
          <div v-else>
            <div class="text-xl font-semibold italic">DAY OFF</div>
          </div>
        </div>
      </div>
    </div>

    <Teleport to="body">
      <!-- use the modal component, pass in the prop -->
      <CreateBookingModal
        :show="modalRef.show"
        :date="modalRef.date"
        :time="modalRef.time"
        @close="toggleModal(false, null, null)"
        @bookingCreated="created($event)"
      >
        <template #header>
          <h3>custom header</h3>
        </template>
      </CreateBookingModal>
    </Teleport>
  </main>
</template>
