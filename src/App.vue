<template>
  <div class="bg-blue-700 min-h-screen">
    <NavComponentsVue></NavComponentsVue>
    <RouterView/>
  </div>
</template>

<script>
  import { RouterView } from 'vue-router';
import NavComponentsVue from './components/NavComponents.vue';
  export default {
    components: {
      RouterView,NavComponentsVue
    },
  }
</script>

<style lang="stylus" scoped>

</style>
