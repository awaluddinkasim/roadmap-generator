<script setup>
import { Modal } from 'flowbite';
import { defineProps, defineEmits, ref } from 'vue';

const props = defineProps(['connections', 'nodes']);

const emits = defineEmits(['addConnection', 'removeConnection']);

const connection = ref({
  from: '',
  label: '',
  target: ''
})

const addConnection = () => {
  emits('addConnection', connection.value)
  connection.value = {
    from: '',
    label: '',
    target: ''
  }

  const targetModal = document.getElementById('connection-modal')
  const modal = new Modal(targetModal)
  modal.hide()
}

</script>


<template>
  <!-- Modal toggle -->
  <button data-modal-target="connection-modal" data-modal-toggle="connection-modal"
    class="block text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800"
    type="button">
    New Connection
  </button>

  <!-- Main modal -->
  <div id="connection-modal" tabindex="-1" aria-hidden="true"
    class="hidden overflow-y-auto overflow-x-hidden fixed top-0 right-0 left-0 z-50 justify-center items-center w-full md:inset-0 h-[calc(100%-1rem)] max-h-full">
    <div class="relative p-4 w-full max-w-md max-h-full">
      <!-- Modal content -->
      <div class="relative bg-white rounded-lg shadow-sm dark:bg-gray-700">
        <!-- Modal header -->
        <div
          class="flex items-center justify-between p-4 md:p-5 border-b rounded-t dark:border-gray-600 border-gray-200">
          <h3 class="text-lg font-semibold text-gray-900 dark:text-white">
            Create New connection
          </h3>
          <button type="button"
            class="text-gray-400 bg-transparent hover:bg-gray-200 hover:text-gray-900 rounded-lg text-sm w-8 h-8 ms-auto inline-flex justify-center items-center dark:hover:bg-gray-600 dark:hover:text-white"
            data-modal-toggle="connection-modal">
            <svg class="w-3 h-3" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 14 14">
              <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="m1 1 6 6m0 0 6 6M7 7l6-6M7 7l-6 6" />
            </svg>
            <span class="sr-only">Close modal</span>
          </button>
        </div>

        <!-- Modal body -->
        <form class="p-4 md:p-5" autocomplete="off" @submit.prevent="addConnection">
          <div class="mb-4">
            <label for="from" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">From</label>
            <select id="from" required v-model="connection.from"
              class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500">
              <option value="" selected>Choose</option>
              <option :value="item.id" v-for="item in props.nodes" :key="item.id">{{ item.label }}</option>
            </select>
          </div>
          <div class="mb-4">
            <label for="connection-label"
              class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Connection Label (optional)</label>
            <input type="text" name="connection_label" id="connection-label" v-model="connection.label"
              class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-primary-600 focus:border-primary-600 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white dark:focus:ring-primary-500 dark:focus:border-primary-500"
              placeholder="Type connection label">
          </div>
          <div class="mb-4">
            <label for="target" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Target</label>
            <select id="target" required v-model="connection.target"
              class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500">
              <option value="" selected>Choose</option>
              <option :value="item.id" v-for="item in props.nodes" :key="item.id">{{ item.label }}</option>
            </select>
          </div>

          <button type="submit"
            class="text-white inline-flex mt-4 items-center bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800">
            <svg class="me-1 -ms-1 w-5 h-5" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg">
              <path fill-rule="evenodd"
                d="M10 5a1 1 0 011 1v3h3a1 1 0 110 2h-3v3a1 1 0 11-2 0v-3H6a1 1 0 110-2h3V6a1 1 0 011-1z"
                clip-rule="evenodd"></path>
            </svg>
            Add connection
          </button>
        </form>
      </div>
    </div>
  </div>


  <div class="text-gray-500">
    <div
      class="w-full p-4 bg-white mt-3 border border-gray-200 rounded-lg shadow-sm sm:p-8 dark:bg-gray-800 dark:border-gray-700">
      <div class="flow-root">
        <template v-if="props.connections.length > 0">
          <ul role="list" class="divide-y divide-gray-200 dark:divide-gray-700">
            <li class="py-3 sm:py-4" v-for="(item, index) in props.connections" :key="index">
              <div class="flex items-center">
                <div class="flex-1 min-w-0 ms-4">
                  <p class="text-sm font-medium text-gray-900 truncate dark:text-white">
                    {{ item.from }} -> {{ item.target }}
                  </p>
                  <p class="text-sm text-gray-500 truncate dark:text-gray-400" v-if="item.label">
                    {{ item.label }}
                  </p>
                </div>
                <div class="inline-flex items-center text-base font-semibold text-gray-900 dark:text-white">
                  <button type="button" @click="emits('removeConnection', item)"
                    class="px-4 py-1 text-sm font-medium text-white bg-red-700 rounded-lg hover:bg-red-800 focus:ring-4 focus:outline-none focus:ring-blue-300 dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800">
                    Delete
                  </button>
                </div>
              </div>
            </li>
          </ul>
        </template>
        <template v-else>
          <p class="text-sm text-gray-500 dark:text-gray-400 text-center">
            No connections found
          </p>
        </template>
      </div>
    </div>
  </div>
</template>