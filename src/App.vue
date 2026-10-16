<script setup>
import AmbientBackground from "./components/AmbientBackground.vue";
import Navbar from "./components/Navbar.vue";
import Footer from "./components/Footer.vue";
import ScrollTop from "./components/ScrollTop.vue";
</script>

<template>
  <div class="relative min-h-screen text-foreground">
    <AmbientBackground />
    <Navbar />
    <router-view />
    <Footer />
    <ScrollTop />
  </div>
</template>
