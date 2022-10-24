<template>
  <a-layout style="min-height: 100vh">
  <navLeft :isShow="isShow" @navChange="navChange"/>
    <transition name="back" mode="out-in">
      <homepage v-if="isShow==1"/>
    </transition>
<!--    <transition name="back" mode="out-in">-->
    <book v-if="isShow==2"/>
<!--    </transition>-->
<!--    <Transition name="fade" mode="out-in">-->
<!--      <component :is="activeComponent"></component>-->
<!--    </Transition>-->
  </a-layout>
</template>

<script>
import book from './components/book.vue'
import navLeft from './components/nav'
import homepage from "@/components/homepage";
import {ref} from "vue";
export default {
  name: 'App',
  components: {
    navLeft,
    book,
    homepage,
  },
  setup(){
    let isShow=ref('2')
    function navChange(value){
      isShow.value=value
    }
    return{
      isShow,
      navChange
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
.back-enter-active,
.back-leave-active {
  transition: opacity 1s ease;
}

.back-enter-from,
.back-leave-to {
  opacity: 0;
}
</style>
