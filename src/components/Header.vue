<script setup>
    import { ref } from 'vue';
    const name = ref("Player")
</script>

<template>
    <header>
        <h1>Hello, {{ name }} and welcome to snake online</h1>
    </header>
</template>

<style scoped>
header{
    display: flex;
    justify-content: center;
    color: brown;
    background-color: rgba(0,0,0,0.5);
    width: 100%;
    border-color: 10px 10px 0 white;
}
</style>