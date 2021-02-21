<template>
  <rili :date='date' @changeSelectDate='changeSelectDate'></rili>
</template>

<script>
import rili from './rili'
export default {
  data () {
    return {
      date : new Date()
    }
  },
  methods: {
    changeSelectDate(date) {
      this.date = date
    }
  },
  components: {
    rili
  }

}
</script>

<style>
  
</style>