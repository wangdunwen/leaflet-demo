<template>
  <div>
  </div>
</template>

<script>
export default {
  name: 'main',
  data () {
    return {
    }
  },
  components: {
  },
  mounted() { 
    this.$router.push({name: 'leaflet'});
  }
}
</script>

<style scoped>
</style>