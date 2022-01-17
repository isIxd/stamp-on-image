<template>
  <StampOnImage />
</template>

<script>
import StampOnImage from './components/StampOnImage.vue'

export default {
  name: 'App',
  components: {
    StampOnImage,
  },
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  // margin-top: 60px;
}
</style>
