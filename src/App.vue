<template>
  <input style="width: 200px; height: 30px;" v-model="strFilter" type="input" placeholder="Search..." autofocus />
  <hr />
  <div class="flex-container">
    <States :states="states" :handleSelectItem="handleSelectItem" />
    <States :states="filteredStates" :handleSelectItem="handleSelectItem" />
  </div>
  <hr />
  <Suspense timeout="0" v-if="stateSelected">
    <State :state="stateSelected" :key="stateSelected.idstate" />
    <template #fallback>
      <div class="loadingBox">Loading...</div>
    </template>
  </Suspense>
  <div v-else></div>
</template>

<script>
import States from './components/States.vue'
import State from './components/State.vue'
import { ref, computed } from 'vue'

//const hostname = process.env.API_ENDPOINT ? 
const hostname = "http://95.111.230.5:9092/api/"
const states = ref(null)
const stateSelected = ref(null)
const strFilter = ref("")

const filteredStates = computed(() => {
  if (!strFilter.value) {
    return states.value
  }
  return states.value.filter(state => state.state.toLowerCase().includes(strFilter.value.toLowerCase()));
})

const handleSelectItem = (item) => {
  stateSelected.value = item;
}

export default {
  name: 'App',
  components: { States, State },
  setup() {
    return {
      states, strFilter, filteredStates, stateSelected, handleSelectItem, hostname
    }
  },
  created() {
    this.fetchData();
  },
  methods: {
    fetchData() {
      fetch(`${hostname}states`).then((response) => {
        response.json().then((data) => {
          data.sort(function (a, b) {
            if (a.state < b.state) { return -1; }
            if (a.state > b.state) { return 1; }
            return 0;
          });
          states.value = data;
        });
      }).catch((err) => {
        console.error(err);
      });
    },
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 45px;
}

.boxScrollable {
  width: 500px;
  height: 350px;
  overflow-y: scroll;
}

.boxScrollable>div {
  cursor: pointer;
}

.flex-container {
  display: flex;
  align-items: center;
  justify-content: center;
}

.flex-container>div {
  background-color: #f1f1f1;
  margin: 10px;
  padding: 16px;
  font-size: 26px;
}

.loadingBox {
  font-size: 32px;
  color: orangered;
}
</style>
