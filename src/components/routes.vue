<template>
  <div class="showcase">
    <div class="unit" v-for="route in renderedRouteChains" v-bind:key="route.UUID">
      <div class="row-cell">
        <div class="title">{{route.totalPrice}}$: {{route.nodeCount}} nodes</div>
      </div>
      <div class="row-cell">
        <div class="route">
          <div v-for="price in route.prices" class="price" v-bind:key="price">
            {{price}}$
          </div>
        </div>
        <div class="route">
          <div v-for="provider in route.providers" class="provider" v-bind:key="provider">
            {{provider}}
          </div>
        </div>
        <div class="route">
          <div v-for="node in route.nodes" class="node-wrapper" v-bind:key="node">
            <div class="node">
            {{node}}
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="pagebtnmenu">
      <b-button @click="currentPage = n" class="pagebtn" v-for="n in pagesCount" v-bind:key="n" variant="outline-primary">{{n}}</b-button>
    </div>
  </div>
</template>

<script>
import { bus } from '../main'

export default {
  name: 'routes',
  props: {
    routeChains: Array,
  },
  data () {
    return {
      nodeFilter: [2, 3, 4],
      currentPage: 1
    }
  },
  computed: {
    filteredRouteChains: function() {
      let routeChains = this.routeChains;
      let filteredRouteChains = [];

      for (let i = 0; i<routeChains.length; i++) {
        if (!(this.nodeFilter.indexOf(routeChains[i].nodeCount)==-1)) {
          filteredRouteChains.push(routeChains[i]);
        }
      }

      filteredRouteChains.sort(function (a,b) {
        if (a.totalPrice>b.totalPrice) {
          return 1;
        }
        if (a.totalPrice<b.totalPrice) {
          return -1;
        }
        return 0;
      });

      return filteredRouteChains;
    },
    renderedRouteChains: function() {
      let routeChains = this.filteredRouteChains;
      let renderedRouteChains = [];

      for (let i = (this.currentPage-1)*4; i<this.currentPage*4; i++) {
        renderedRouteChains.push(routeChains[i]);
      }
      if (!renderedRouteChains[0]) {
        return 0;
      }
      return renderedRouteChains;
    },
    pagesCount: function() {
      let filteredRouteChains = this.filteredRouteChains;
      return filteredRouteChains.length/4;
    }
  },
  created() {
    bus.$on('tweakFilter', (filter) => {
      this.nodeFilter = filter;
      this.currentPage = 1;
    })
    bus.$on('resetPageCount', () => {
      this.currentPage = 1;
    })
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.showcase{ 
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.unit {
  border: 1px solid black;
  display: flex;
  flex-direction: row;
  width: 100%;
  justify-content: space-between;
  margin-bottom: 16px;
  padding: 8px;
}

.route {
  display: flex;
  flex-direction: row;
  justify-content: flex-end;
}

.price, .provider, .node {
  padding: 8px;
  width: 72px;
  border-radius: 8px;
}

.price {
  transition: 0.4s;
  border: 1px solid #28a745;
}

.price:hover {
  background-color: #28a745;
  color: white;
  transition: 0.4s;
  cursor: pointer;
}

.provider, .price {
  margin-right: 76px;
}

.provider {
  border: 1px solid #17a2b8;
  transition: 0.4s;
  margin-top: 4px;
}

.provider:hover {
  transition: 0.4s;
  color: white;
  background-color: #17a2b8;
  cursor: pointer;
}

.node {
  transition: 0.4s;
  background-color: #6c757d;
  border-color: #6c757d;
  border-radius: 12px;
  color: white;
  margin-right: 4px;
  width: 72px;
}

.node-wrapper {
  display: flex;
  flex-direction: row;
}

.node-wrapper:before {
  content: "--->";
  width: 72px;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  font-weight: 900;
}

.node-wrapper:first-child:before {
  content: " ";
}

.node:hover {
  transition: 0.4s;
  background-color: #4a5157;
  cursor: pointer;
}

.row {
  border: 1px solid grey;
  border-radius: 16px;
  margin: 16px;
}

.pagebtn {
  margin: 8px;
}
</style>
