<script setup>
import Footer from '@/components/Footer.vue';
import Header from '@/components/Header.vue';
import Main from '@/components/main.vue';

</script>

<template>
  <Header></Header>
  <Main></Main>
  <Footer></Footer>
</template>

<style>
  body{
    background-color: bisque;
    height: 95vh;
  }
  #app{
    display: flex;
    flex-direction: column;
    align-items: center;
    height: 100%;
  }
</style>
