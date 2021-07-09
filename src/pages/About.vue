<template>
  <div>
    <h1>{{ title }}</h1>
    
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  setup() {
    const title = ref('About Page')

    return {
      title
    }
  }
}
</script>

<style lang="scss" scoped>

</style>