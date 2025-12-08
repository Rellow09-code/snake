<script setup>
    import { ref } from 'vue';
    const year = ref("2025")
</script>

<template>
    <footer>
        <p id="footer">&copy; Netshaulu-technologies {{ year }}</p>
    </footer>
</template>

<style scoped>
    #footer{
        color: brown;
    }
</style>