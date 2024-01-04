<template>
  <div id="app">
    <!-- <img alt="Vue logo" :src="img"> -->
     <MAP  />
    <!-- <olmap /> -->
  </div>
</template>

<script>
// import olmap from './components/olmap.vue'
import MAP from './components/maptest.vue'
export default {
  name: 'app',
  data:function(){
    return{
      img:'./img/logo.png'
    }
    
  },
  components: {
    // olmap,
    MAP
  }
}
</script>

<style>
*{padding:0; margin:0;}
html,body{
  height: 100%;
}
#app {
  height: 100%;
}
</style>
