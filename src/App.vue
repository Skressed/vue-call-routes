<template>
  <div id="app">
    <HandlingForm v-bind:selectedSRC="selectedSRC" v-bind:selectedDEST="selectedDEST" v-bind:countries="countries"></HandlingForm>
    <routes v-bind:routeChains="routeChains"></routes>
  </div>
</template>

<script>
import { uuid } from 'vue-uuid'
import Routes from './components/routes.vue'
import HandlingForm from './components/handlingform.vue'
import mockedData from './fixtures/call-paths.json'
import { bus } from './main'

export default {
  name: 'App',
  data () {
    return {
      routes: [],
      countries: Object,
      selectedSRC: '',
      selectedDEST: '',
      routeChains: [],
      uuid: uuid.v1()
    }
  },
  components: {
    Routes, HandlingForm
  },
  created() {
    this.loadMockedData();
    bus.$on('renderPossibleRoutes', (src, dest) => {
      this.selectedSRC = src;
      this.selectedDEST = dest;
      this.renderPossibleRoutes();
    })
  },
  methods: {
    loadMockedData() {
      this.countries = mockedData.data.country;
      let routes = mockedData.data.company;

      for (let key in routes) {
        for (let i=0; i<routes[key].length; i++) {
          let route = routes[key][i];
          route.provider = key;
          this.routes.push(route);
        }
      }
    },

    renderPossibleRoutes() {
      if (this.selectedSRC && this.selectedDEST) {
        this.routeChains = [];
        this.addNode();
        bus.$emit('resetPageCount');
      }
    },

    addNode(prevNode, prices, providers, nodes) {
      for (let n=0; n<this.routes.length; n++) {
        let node = this.routes[n];

        if(!prevNode) {
          if (node.src === this.selectedSRC) {
            let prices = [node.price];
            let providers = [node.provider];
            let nodes = [node.src, node.des];

            this.tryNewRoute(node, nodes, prices, providers);
          }
        }
        else if (node.src === prevNode.des && nodes.indexOf(node.des) == -1) {
          prices.push(node.price);
          providers.push(node.provider);
          nodes.push(node.des);

          this.tryNewRoute(node, nodes, prices, providers);
        }
      }
    },

    tryNewRoute(node, nodes, prices, providers) {
      if (node.des === this.selectedDEST) {
        this.pushNewRoute(nodes, prices, providers, nodes.length);
      }
      else {
        this.addNode(node, prices, providers, nodes);
      }
    },

    pushNewRoute(nodes, prices, providers, nodeCount) {
      let totalPrice = 0;
      for (let i=0; i<prices.length; i++) {
        totalPrice += parseInt(prices[i], 10);
      }

      this.routeChains.push({
        nodes: nodes,
        prices: prices,
        providers: providers,
        nodeCount: nodeCount,
        totalPrice: totalPrice,
        UUID: this.$uuid.v1()
      });
    }
  }
}
</script>

<style>
#app {
  display: flex;
  flex-direction: row;
  justify-content: space-around;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.panel, .showcase {
  width: 40%;
}
</style>
