<template>
  <ListQuotes :quotes="quotes" :listen-quotes="listenQuotes" @unListen="onUnListen" />
  <div class="mt-2 text-right">
      <cite class="text-small">
        Atualizará novamente em <b>{{nextUpdateTime}} segundos</b>
    </cite>
  </div>
</template>

<script>
import { onMounted, onUnmounted, reactive, ref, toRefs, watch } from 'vue'
import ListQuotes from './ListQuotes.vue'
import api from '@/services/api'

const CURRENT_UPDATE_TIME = 30

export default {
    props: {
        listenQuotes: {
            type: Array,
            required: true
        }
    },
    components: {ListQuotes},
    emits: ['unListen'],
    setup(props, { emit }) {
        const interval = ref(null)
        const quotes = ref({})
        const nextUpdateTime = ref(CURRENT_UPDATE_TIME)

        const methods = reactive({
            onUnListen(code) {
                emit('unListen', code)
            },
            restartInterval() {
                clearInterval(interval.value)
                nextUpdateTime.value = CURRENT_UPDATE_TIME
                interval.value = setInterval(() => {
                    nextUpdateTime.value -= 1
                    if (nextUpdateTime.value === 0) {
                        nextUpdateTime.value = CURRENT_UPDATE_TIME
                        this.refresh()
                    }
                }, 1000)
            },
            async refresh() {
                const { data } = await api.listen(props.listenQuotes)
                quotes.value = data
            },
        })

        onMounted(() => {
            methods.refresh()
            methods.restartInterval()
        })

        onUnmounted(() => {
            clearInterval(interval.value)
        })

        watch(props, () => {
            methods.refresh()
            methods.restartInterval()
        })

        return {
            ...toRefs(methods),
            quotes,
            nextUpdateTime
        }
    }
}
</script>

<style>

</style>