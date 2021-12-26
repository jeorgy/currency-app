<template>
  <div class="container grid-lg my-2 py-2">
    <div class="card mb-2" v-if="listenQuotes.length > 0">
      <div class="card-header">
        <div class="h4">Acompanhando</div>
      </div>
      <div class="card-body">
        <WatchListQuotes :listen-quotes="listenQuotes" @unListen="onUnListen" />
      </div>
    </div>
    <div class="card">
      <div class="card-header">
        <div class="h4">Todas as moedas</div>
      </div>
      <div class="card-body">
        <ListQuotes
          :quotes="quotes"
          :listen-quotes="listenQuotes"
          @listen="onListen"
          @unListen="onUnListen" />
      </div>
    </div>
  </div>
</template>

<script>
import 'spectre.css/dist/spectre.min.css'
import 'spectre.css/dist/spectre-icons.min.css'
import { reactive, toRefs } from '@vue/reactivity'
import api from '@/services/api'
import { onMounted } from '@vue/runtime-core'
import ListQuotes from './components/ListQuotes.vue'
import WatchListQuotes from './components/WatchListQuotes.vue'

export default {
  name: 'App',
  components: {
    ListQuotes,
    WatchListQuotes
  },
  setup() {
    const data = reactive({
      quotes: {},
      listenQuotes: []
    })

    onMounted(async () => {
      const response = await api.all()
      data.quotes = response.data
      
    })

    function onListen(code) {
      data.listenQuotes.push(code)
    }
    function onUnListen(code) {
      data.listenQuotes = data.listenQuotes.filter(key => key != code)
    }

    return {
      ...toRefs(data),
      onListen,
      onUnListen
    }
  }
}
</script>

<style>
</style>
