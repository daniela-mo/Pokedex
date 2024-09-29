<template>
  <div>
    <Header />
    <RouterView />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import Header from './components/Header.vue'

export default defineComponent({
  setup() {
    return {}
  },
  components: { Header }
})
</script>

<style scoped></style>
