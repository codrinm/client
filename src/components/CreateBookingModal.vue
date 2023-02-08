<script setup>
import { reactive, computed, ref, onMounted } from 'vue';
import axios from 'axios';
import { toast } from 'vue3-toastify';

const emit = defineEmits(['booked']);

const props = defineProps({
  show: Boolean,
  date: Date,
  time: String,
});

const initialFormState = {
  name: '',
  email: '',
  phone: '',
  make: '',
  model: '',
};

const form = reactive({
  ...initialFormState,
});

const formCanBeSubmitted = computed(() => {
  return (
    form.name != '' &&
    form.email != '' &&
    // form.phone != '' &&
    form.make != '' &&
    form.model != ''
  );
});

const vehicles = ref([]);
const models = computed(() => {
  const filtered = vehicles.value.find((v) => v.id === form.make);
  return filtered ? filtered.models : [];
});

const onSubmit = () => {
  const formData = { ...form, date: props.date, time: props.time };

  axios
    .post('http://localhost:8000/api/bookings', formData)
    .then((res) => {
      emit('bookingCreated', formData);
      Object.assign(form, initialFormState);
      toast.success('Booking was created!');
    })
    .catch((err) =>
      toast.error(
        err.response?.data?.message ||
          'An error occured, please try again MODAL!'
      )
    );
};

const getVehicles = () => {
  axios
    .get('http://localhost:8000/api/vehicles')
    .then((res) => (vehicles.value = res.data))
    .catch((err) =>
      toast.error(err.message || 'An error occured, please try again!')
    );
};

onMounted(() => getVehicles());
</script>

<template>
  <Transition name="modal">
    <div v-if="show" class="modal-mask">
      <div
        role="alert"
        class="container mx-auto w-11/12 md:w-2/3 max-w-lg absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2"
      >
        <div
          class="relative py-8 px-5 md:px-10 bg-white shadow-md rounded border border-gray-400"
        >
          <h1
            class="text-gray-800 font-xl font-bold tracking-normal leading-tight mb-4"
          >
            Create Booking
          </h1>
          <form @submit.prevent="onSubmit">
            <label
              for="datetime"
              class="text-gray-800 text-sm font-bold leading-tight tracking-normal"
              >Date and time *</label
            >
            <input
              id="datetime"
              class="mb-5 mt-2 text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-3 text-sm border-gray-300 rounded border"
              disabled="disabled"
              :value="date + ' ' + time"
            />
            <label
              for="name"
              class="text-gray-800 text-sm font-bold leading-tight tracking-normal"
              >Name *</label
            >
            <input
              id="name"
              class="mb-5 mt-2 text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-3 text-sm border-gray-300 rounded border"
              placeholder="Full name"
              v-model="form.name"
            />
            <label
              for="email"
              class="text-gray-800 text-sm font-bold leading-tight tracking-normal"
              >Email *</label
            >
            <input
              id="email"
              class="mb-5 mt-2 text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-3 text-sm border-gray-300 rounded border"
              placeholder="Email address"
              v-model="form.email"
            />
            <label
              for="phone"
              class="text-gray-800 text-sm font-bold leading-tight tracking-normal"
              >Phone</label
            >
            <input
              id="phone"
              class="mb-5 mt-2 text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-3 text-sm border-gray-300 rounded border"
              placeholder="Phone number"
              v-model="form.phone"
            />
            <label
              for="make"
              class="text-gray-800 text-sm font-bold leading-tight tracking-normal"
              >Vehicle Make *</label
            >
            <select
              id="make"
              class="mb-5 mt-2 text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-3 text-sm border-gray-300 rounded border"
              placeholder="Phone number"
              v-model="form.make"
            >
              <option></option>
              <option v-for="make in vehicles" :key="make.id" :value="make.id">
                {{ make.make }}
              </option>
            </select>
            <label
              for="model"
              class="text-gray-800 text-sm font-bold leading-tight tracking-normal"
              >Vehicle Model *</label
            >
            <select
              id="model"
              class="mb-5 mt-2 text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-3 text-sm border-gray-300 rounded border"
              placeholder="Phone number"
              v-model="form.model"
            >
              <option></option>
              <option v-for="model in models" :key="model.id" :value="model.id">
                {{ model.model }}
              </option>
            </select>

            <div class="flex items-center justify-start w-full">
              <button
                class="w-1/2 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-700 transition duration-150 ease-in-out hover:bg-indigo-600 bg-indigo-700 rounded text-white px-8 py-2 text-sm disabled:opacity-50 disabled:cursor-not-allowed"
                :disabled="!formCanBeSubmitted"
              >
                Create Booking
              </button>
              <button
                type="button"
                class="w-1/2 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-gray-400 ml-3 bg-gray-100 transition duration-150 text-gray-600 ease-in-out hover:border-gray-400 hover:bg-gray-300 border rounded px-8 py-2 text-sm"
                @click="$emit('close')"
              >
                Cancel
              </button>
            </div>
            <button
              type="button"
              class="cursor-pointer absolute top-0 right-0 mt-4 mr-5 text-gray-400 hover:text-gray-600 transition duration-150 ease-in-out rounded focus:ring-2 focus:outline-none focus:ring-gray-600"
              @click="$emit('close')"
              aria-label="close modal"
              role="button"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                class="icon icon-tabler icon-tabler-x"
                width="20"
                height="20"
                viewBox="0 0 24 24"
                stroke-width="2.5"
                stroke="currentColor"
                fill="none"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <path stroke="none" d="M0 0h24v24H0z" />
                <line x1="18" y1="6" x2="6" y2="18" />
                <line x1="6" y1="6" x2="18" y2="18" />
              </svg>
            </button>
          </form>
        </div>
      </div>
    </div>
  </Transition>
</template>

<style>
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  transition: opacity 0.3s ease;
}

.modal-container {
  width: 300px;
  margin: auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
}

.modal-header h3 {
  margin-top: 0;
  color: #42b983;
}

.modal-body {
  margin: 20px 0;
}

.modal-default-button {
  float: right;
}

/*
 * The following styles are auto-applied to elements with
 * transition="modal" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the modal transition by editing
 * these styles.
 */

.modal-enter-from {
  opacity: 0;
}

.modal-leave-to {
  opacity: 0;
}

.modal-enter-from .modal-container,
.modal-leave-to .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
</style>
