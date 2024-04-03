<script>
const hostname = "http://localhost:8888/api/"
export default {
  name: 'StateItem',
  props: {
    state: Object,
  },
  async setup(props) {
    const res = await fetch(`${hostname}states/${props.state.idstate}`)
    const stateFetched = await res.json()
    stateFetched.sort(function (a, b) {
      if (a.county < b.county) { return -1; }
      if (a.county > b.county) { return 1; }
      return 0;
    });
    const sumPopulation = stateFetched.reduce((accumulator, currentValue) => accumulator + currentValue.population, 0);
    const arePopulationEquals = sumPopulation === props.state.population;
    return {
      stateFetched, arePopulationEquals, sumPopulation
    }
  }
}
</script>

<template>
  <div class="flex-container">
    <div>
      Name: {{ state.state }} <br />
      Population: {{ state.population }} <br />
      # Countries: {{ state.counties }} <br />
      Sum population from countries: {{ sumPopulation }} <br />
      Are population equals: {{ arePopulationEquals ? 'Yes' : 'No' }}
    </div>
    <div class="boxScrollable">
      <div v-for="item in stateFetched" :key="item.idcountry">
        {{ item.county }} - {{ item.population }}
      </div>
    </div>
  </div>
</template>