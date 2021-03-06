<template>
  <div
    class="relative flex flex-col min-w-0 break-words w-full mb-6 shadow-lg rounded"
    :class="[color === 'light' ? 'bg-white' : 'bg-green-900 text-white']"
  >
    <div class="rounded-t mb-0 px-4 py-3 border-0">
      <div class="flex flex-wrap items-center">
        <div class="relative w-full px-4 max-w-full flex-grow flex-1">
          <h3
            class="font-semibold text-lg"
            :class="[color === 'light' ? 'text-gray-800' : 'text-white']"
          >
            Nodes
          </h3>
        </div>
      </div>
    </div>
    <div class="block w-full overflow-x-auto">
      <!-- Projects table -->
      <table class="items-center w-full bg-transparent border-collapse">
        <thead>
          <tr>
            <th
              class="px-6 align-middle border border-solid py-3 text-xs uppercase border-l-0 border-r-0 whitespace-no-wrap font-semibold text-left"
              :class="color === 'light' ? 'bg-gray-100 text-gray-600 border-gray-200' : 'bg-green-800 text-green-300 border-green-700'"
            >
              <span class="ml-3">
                Node
              </span>
            </th>
            <th
              class="px-6 align-middle border border-solid py-3 text-xs uppercase border-l-0 border-r-0 whitespace-no-wrap font-semibold text-left"
              :class="color === 'light' ? 'bg-gray-100 text-gray-600 border-gray-200' : 'bg-green-800 text-green-300 border-green-700'"
            >
              Last update
            </th>
            <th
              class="px-6 align-middle border border-solid py-3 text-xs uppercase border-l-0 border-r-0 whitespace-no-wrap font-semibold text-left"
              :class="color === 'light' ? 'bg-gray-100 text-gray-600 border-gray-200' : 'bg-green-800 text-green-300 border-green-700'"
            >
              Score
            </th>
            <th
              class="px-6 align-middle border border-solid py-3 text-xs uppercase border-l-0 border-r-0 whitespace-no-wrap font-semibold text-left"
              :class="color === 'light' ? 'bg-gray-100 text-gray-600 border-gray-200' : 'bg-green-800 text-green-300 border-green-700'"
            >
              Tests
            </th>
            <th
              class="px-6 align-middle border border-solid py-3 text-xs uppercase border-l-0 border-r-0 whitespace-no-wrap font-semibold text-left"
              :class="color === 'light' ? 'bg-gray-100 text-gray-600 border-gray-200' : 'bg-green-800 text-green-300 border-green-700'"
            ></th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="node in nodes" :key="node.name">
            <td
              class="border-t-0 px-6 align-middle border-l-0 border-r-0 text-sm whitespace-no-wrap p-4 text-left flex items-center"
            >
              <span
                class="font-bold ml-3"
                :class="[color === 'light' ? 'text-gray-700' : 'text-white']"
              >
                {{ node.name }}
                <small class="block text-gray-600 font-normal text-xs">{{ node.endpoint }}</small>
              </span>
            </td>
            <td
              class="border-t-0 px-6 align-middle border-l-0 border-r-0 text-sm whitespace-no-wrap p-4"
            >
              {{ minutesAgo(node.updated_at) }}
            </td>
            <td
              class="border-t-0 px-6 align-middle border-l-0 border-r-0 text-sm whitespace-no-wrap p-4"
            >
              <div class="flex items-center">
                <span class="mr-2">{{ node.score }}%</span>
                <div class="relative w-full hidden sm:block">
                  <div
                    class="overflow-hidden h-2 text-sm flex rounded"
                    :class="[
                      { 'bg-green-200': node.score === 100 },
                      { 'bg-yellow-200': node.score > 90 },
                      { 'bg-orange-200': node.score >= 80 },
                      { 'bg-red-200': node.score < 80 }
                    ]">
                    <div
                      :style="`width: ${node.score}%;`"
                      class="shadow-none flex flex-col text-center whitespace-nowrap text-white justify-center"
                      :class="[
                      { 'bg-green-500': node.score === 100 },
                      { 'bg-yellow-500': node.score > 90 },
                      { 'bg-orange-500': node.score >= 80 },
                      { 'bg-red-500': node.score < 80 }
                    ]"></div>
                  </div>
                </div>
              </div>
            </td>
            <td
              class="border-t-0 px-6 align-middle border-l-0 border-r-0 text-sm whitespace-no-wrap p-4"
            >
              <a @click="openNodeModal(node)" class="cursor-pointer">
                <i
                  class="fas fa-circle mr-2"
                  :class="[
                    { 'text-green-500': node.score === 100 },
                    { 'text-yellow-500': node.score > 90 },
                    { 'text-orange-500': node.score >= 80 },
                    { 'text-red-500': node.score < 80 }
                  ]">
                </i>
                {{ node.success }} / {{ node.success + node.fail }}
                <i class="fas fa-search text-lg text-default ml-3"></i>
              </a>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <div v-if="showNodeModal" class="overflow-y-auto fixed inset-0 z-50 outline-none focus:outline-none justify-center items-center sm:flex">
      <div class="relative w-auto mx-auto max-w-6xl">
        <!--content-->
        <div class="border-0 sm:rounded-lg shadow-lg relative flex flex-col w-full bg-white outline-none focus:outline-none">
          <!--header-->
          <div class="flex items-start justify-between p-5 border-b border-solid border-gray-300 rounded-t">
            <h3 class="text-3xl font-semibold">
              {{ selectedNode.name }}
            </h3>
            <button class="p-1 ml-auto bg-transparent border-0 text-black opacity-5 float-right text-3xl leading-none font-semibold outline-none focus:outline-none" @click="closeNodeModal()">
              <span class="bg-transparent text-black opacity-5 h-6 w-6 text-2xl block outline-none focus:outline-none">
                ×
              </span>
            </button>
          </div>
          <!--body-->
          <div class="relative overflow-x-auto flex-auto">
            <table class="items-center w-full bg-transparent border-collapse">
              <thead>
                <tr>
                  <th
                    class="px-6 align-middle border border-solid py-3 text-xs uppercase border-l-0 border-r-0 whitespace-no-wrap font-semibold text-left"
                    :class="color === 'light' ? 'bg-gray-100 text-gray-600 border-gray-200' : 'bg-green-800 text-green-300 border-green-700'"
                  >
                    Name
                  </th>
                  <th
                    class="px-6 align-middle border border-solid py-3 text-xs uppercase border-l-0 border-r-0 whitespace-no-wrap font-semibold text-left"
                    :class="color === 'light' ? 'bg-gray-100 text-gray-600 border-gray-200' : 'bg-green-800 text-green-300 border-green-700'"
                  >
                    Method
                  </th>
                  <th
                    class="px-6 align-middle border border-solid py-3 text-xs uppercase border-l-0 border-r-0 whitespace-no-wrap font-semibold text-left"
                    :class="color === 'light' ? 'bg-gray-100 text-gray-600 border-gray-200' : 'bg-green-800 text-green-300 border-green-700'"
                  >
                    Type
                  </th>
                  <th
                    class="px-6 align-middle border border-solid py-3 text-xs uppercase border-l-0 border-r-0 whitespace-no-wrap font-semibold text-left"
                    :class="color === 'light' ? 'bg-gray-100 text-gray-600 border-gray-200' : 'bg-green-800 text-green-300 border-green-700'"
                  >
                  </th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="test in selectedNode.tests" :key="test.name">
                  <td class="border-t-0 px-6 align-middle border-l-0 border-r-0 text-sm whitespace-no-wrap py-2">
                    {{ test.name }}
                    <i class="far fa-question-circle text-lg text-gray-600 ml-2" :content="test.description || 'No details'" v-tippy='{ arrow : true }'></i>
                  </td>
                  <td class="border-t-0 px-6 align-middle border-l-0 border-r-0 text-sm whitespace-no-wrap py-2">
                    <span
                      class="text-xs font-semibold inline-block py-1 px-2 uppercase rounded uppercase last:mr-0 mr-1"
                      :class="[
                        { 'text-indigo-600 bg-indigo-200': test.type === 'fetch' },
                        { 'text-purple-600 bg-purple-200': test.type === 'cast' },
                        { 'text-gray-600 bg-gray-200': !['fetch', 'cast'].includes(test.type) }
                      ]">
                      {{ test.type }}
                    </span>
                  </td>
                  <td class="border-t-0 px-6 align-middle border-l-0 border-r-0 text-sm whitespace-no-wrap py-2">{{ test.method }}</td>
                  <td class="border-t-0 px-6 align-middle border-l-0 border-r-0 text-sm whitespace-no-wrap py-2"><i class="fas fa-circle mr-2" :class="test.success ? 'text-green-400' : 'text-red-400'"></i></td>
                </tr>
              </tbody>
            </table>
          </div>
          <!--footer-->
          <div class="flex items-center justify-end p-2 border-t border-solid border-gray-300 rounded-b">
            <button class="text-red-500 background-transparent font-bold uppercase px-6 py-2 text-sm outline-none focus:outline-none mr-1 mb-1 ease-linear transition-all duration-150" type="button" @click="closeNodeModal()">
              Close
            </button>
          </div>
        </div>
      </div>
    </div>
    <div v-if="showNodeModal" class="opacity-25 fixed inset-0 z-40 bg-black"></div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  props: {
    color: {
      default: "light",
      validator: function (value) {
        return ["light", "dark"].indexOf(value) !== -1
      },
    },
  },
  data() {
    return {
      allNodes: [],
      selectedNode: null,
      showNodeModal: false
    };
  },
  computed: {
    nodes: function () {
      return [...this.allNodes].sort((a, b) => b.score - a.score)
    }
  },
  methods: {
    minutesAgo: function (date) {
      const now = Date.now()
      const timestamp = new Date(date).getTime()

      return now - timestamp < 0
        ? 'now'
        : `${parseInt((now - timestamp) / 60000)} mins ago`
    },
    openNodeModal: async function(score) {
      const node = await axios.get(`/nodes/${score.name}`)
      this.selectedNode = node.data
      this.showNodeModal = true
    },
    closeNodeModal: function () {
      this.selectedNode = null
      this.showNodeModal = false
    }
  },
  async mounted() {
    const response = await axios.get('/nodes')
    this.allNodes = response.data
  }
}
</script>
