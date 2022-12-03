<script setup>
import { RouterLink, RouterView } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'
import AppMenu from './components/common/appMenu/index.vue'
</script>

<template>
  <main>
    <aside class="l-menu-box">
    <AppMenu></AppMenu>
    </aside>
  </main>

  <RouterView />
</template>

<style lang="css" scoped>
.l-menu-box{
  position: fixed;
  left: 0;
  top:0;
  bottom: 0;
  background-color: #545c64;
}
</style>
