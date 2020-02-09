<template>
  <h1>{{ user.name }}</h1>
</template>

<script>
import { mapState } from 'vuex'

export default {
  middleware: ['chat'],
  head() {
    return {
      title: `Chat - ${this.user.room}`
    }
  },
  computed: mapState(['user'])
}
</script>
