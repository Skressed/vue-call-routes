<template>
  <b-card class="panel">
    <b-form-select @change="renderPossibleRoutes()" v-model="selectedSRC">
      <b-form-select-option disabled value="">Выберите звонящую страну</b-form-select-option>
      <b-form-select-option v-for="(value, name) in countries" v-bind:key="name" v-bind:value="name">{{value}}</b-form-select-option>
    </b-form-select>
    <b-form-select @change="renderPossibleRoutes()" v-model="selectedDEST">
      <b-form-select-option disabled value="">Выберите принимающую страну</b-form-select-option>
      <b-form-select-option v-for="(value, name) in countries" v-bind:key="name" v-bind:value="name">{{value}}</b-form-select-option>
    </b-form-select>
    <div class="formCheckbox">
      <input @change="tweakFilter()" type="checkbox" id="all" v-model="twoNodes" value="Прямое соединение">
      <span class="checkboxTitle">Прямое соединение</span>
    </div>
    <div class="formCheckbox">
      <input @change="tweakFilter()" type="checkbox" id="all" v-model="threeNodes" value="Один дополнительный узел">
      <span class="checkboxTitle">Один дополнительный узел</span>
    </div>
    <div class="formCheckbox">
      <input @change="tweakFilter()" type="checkbox" id="all" v-model="fourNodes" value="Два дополнительных узла">
      <span class="checkboxTitle">Два дополнительных узла</span>
    </div>
  </b-card>
</template>

<script>
import { bus } from '../main'

export default {
    name: 'handlingform',
    props: {
      countries: Object
    },
    data () {
      return {
          selectedSRC: '',
          selectedDEST: '',
          twoNodes: true,
          threeNodes: true,
          fourNodes: true
      }
    },
    methods: {
      tweakFilter() {
        let nodeFilter = [];
        if (this.twoNodes) nodeFilter.push(2) ;
        if (this.threeNodes) nodeFilter.push(3) ;
        if (this.fourNodes) nodeFilter.push(4) ;
        bus.$emit('tweakFilter', nodeFilter);
      },
      renderPossibleRoutes() {
        bus.$emit('renderPossibleRoutes', this.selectedSRC, this.selectedDEST);
      }
    }
}
</script>

<style>
.panel {
  display: flex;
  flex-direction: column;
  max-height: 400px;
}

select {
  margin: 4px 16px !important;
}

.card-body {
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
}

.formCheckbox {
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
  align-items: center;
  height: 32px;
  margin: 4px 16px !important;
}

input[type="checkbox"] {
  width: 24px;
  height: 24px;
  cursor: pointer;
}

.checkboxTitle {
  margin-left: 4px;
  font-size: 18px;
  font-weight: 600;
}
</style>