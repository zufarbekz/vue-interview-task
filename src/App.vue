<template>
  <div id="app">
    <Main :categories="categories" :products="products"/>
  </div>
</template>

<script>
import Main from './components/Main.vue'
import categories from '@/data/Categories.js'
import products from '@/data/Products.js'

export default {
  name: 'App',
  components: {
    Main
  },
  data() {
    return{
      categories,
      products
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
  margin-top: 60px;
}
</style>
