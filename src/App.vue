<template>
  <div id="app">
    <UserPanelComponent />
    <GraphComponent />
  </div>
</template>

<script>
import GraphComponent from './components/GraphComponent.vue'
import UserPanelComponent from './components/UserPanelComponent'
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)
const store = new Vuex.Store({
  state: {
    answer: "Calculate max flow"
  },
  mutations: {
    getAnswer(state, newText) {
      state.answer = newText
    }
  },
  actions: {
    getAnswer: function (context) {
       fetch("http://localhost:8080/maxFlow/getmaxflow", {mode: "cors"})
              .then(
                      (response) => {
                        response.text().then((text) => {
                                  context.commit("getAnswer", text)
                                }
                        )
                      })
    }
  }
})

export default {
  name: 'App',
  components: {
    GraphComponent,
    UserPanelComponent
  },
  store
}
</script>

<style>
#app, html, body {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  width: 100%;
  height: 100%;
  margin: 0;
  padding: 0;
}
</style>
