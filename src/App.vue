<script setup>
import { onMounted, ref, onBeforeMount } from 'vue'
import { initFlowbite } from 'flowbite'
import Nodes from './components/Nodes.vue'
import Connections from './components/Connections.vue'
import Diagram from './components/Diagram.vue'

onBeforeMount(() => {
  loadLocalStorage()
})

onMounted(() => {
  initFlowbite()
})

const loadLocalStorage = () => {
  localStorage.getItem('nodes') && (nodes.value = JSON.parse(localStorage.getItem('nodes')))
  localStorage.getItem('connections') && (connections.value = JSON.parse(localStorage.getItem('connections')))

  updateDiagram()
}

const code = ref('graph TD\n');

const nodes = ref([])
const connections = ref([])

const addNode = (node) => {
  if (nodes.value.find(n => n.id === node.id)) {
    return
  }

  nodes.value.push(node)
  updateDiagram()
}

const removeNode = (node) => {
  connections.value = connections.value.filter(c => !(c.from === node.id || c.target === node.id))

  nodes.value = nodes.value.filter(n => n.id !== node.id)
  updateDiagram()
}

const addConnection = (connection) => {
  if (connections.value.find(c => c.from === connection.from && c.target === connection.target)) {
    return
  }
  connections.value.push(connection)
  updateDiagram()
}

const removeConnection = (connection) => {
  connections.value = connections.value.filter(c => c.from !== connection.from && c.target !== connection.target)
  updateDiagram()
}

const updateDiagram = () => {
  let diagram = 'graph TD\n';

  if (nodes.value.length > 0) {
    nodes.value.forEach(node => {
      diagram += `    ${node.id}[${node.label}]\n`;
    })
  }

  if (connections.value.length > 0) {
    connections.value.forEach(conn => {
      if (conn.label) {
        diagram += `    ${conn.from} -->|${conn.label}| ${conn.target}\n`;
      } else {
        diagram += `    ${conn.from} --> ${conn.target}\n`;
      }
    })
  }

  code.value = diagram

  localStorage.setItem('nodes', JSON.stringify(nodes.value))
  localStorage.setItem('connections', JSON.stringify(connections.value))
}

</script>

<template>

  <div class="min-h-screen">
    <div class="lg:grid grid-cols-5 xl:grid-cols-7 gap-4 py-9">

      <div class="col-span-2">

        <div class="rounded-xl p-6 mx-5 bg-gray-100">

          <div class="mb-4 border-b border-gray-200 dark:border-gray-700">
            <ul class="flex flex-wrap -mb-px text-sm font-medium text-center" id="default-tab"
              data-tabs-toggle="#default-tab-content" role="tablist"
              data-tabs-inactive-classes="text-gray-600 hover:text-gray-600 dark:text-gray-500 dark:hover:text-gray-500 border-gray-300 dark:border-gray-400 cursor-pointer">
              <li class="me-2" role="presentation">
                <button class="inline-block p-4 border-b-2 rounded-t-lg" id="nodes-tab" data-tabs-target="#nodes"
                  type="button" role="tab" aria-controls="nodes" aria-selected="false">Nodes</button>
              </li>
              <li class="me-2" role="presentation">
                <button
                  class="inline-block p-4 border-b-2 rounded-t-lg hover:text-gray-600 hover:border-gray-300 dark:hover:text-gray-300"
                  id="connections-tab" data-tabs-target="#connections" type="button" role="tab"
                  aria-controls="connections" aria-selected="false">Connections</button>
              </li>
            </ul>
          </div>
          <div id="default-tab-content">
            <div class="hidden p-4 rounded-lg bg-gray-200 dark:bg-gray-800" id="nodes" role="tabpanel"
              aria-labelledby="nodes-tab">
              <Nodes :nodes="nodes" @addNode="addNode" @removeNode="removeNode" />
            </div>
            <div class="hidden p-4 rounded-lg bg-gray-200 dark:bg-gray-800" id="connections" role="tabpanel"
              aria-labelledby="connections-tab">
              <Connections :connections="connections" :nodes="nodes" @addConnection="addConnection"
                @removeConnection="removeConnection" />
            </div>
          </div>
        </div>

      </div>

      <div class="col-span-3 xl:col-span-5 py-9">
        <Diagram :code="code" />
      </div>

    </div>
  </div>

  <footer class="p-4 bg-white rounded-lg shadow md:px-6 md:py-8 dark:bg-gray-800">
    <span class="block text-sm text-gray-500 sm:text-center dark:text-gray-400">
      © 2025 <span class="font-semibold">Roadmap Generator</span>. All Rights Reserved.
    </span>
  </footer>
</template>