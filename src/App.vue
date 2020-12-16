<template>
  <div id="app">
    <Home99></Home99>
<!--    <router-view/>-->
  </div>
</template>

<script>
  import Home99 from './views/Home99'

  export default {

    components:{
      Home99
    },
    data(){
      return{
        active:1,
      }
    }

  }

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
</style>
