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
    color: brown;
}
</style>