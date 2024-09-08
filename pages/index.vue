<template>
  <!-- Navbar -->
  <Navbar />

  <!-- hero section -->
  <Hero />

  <!-- Projects -->
  <Projects />

  <!-- Blogs -->
  <Blogs />
</template>

<script setup>
import { ref } from "vue";
import Navbar from "/components/Navbar.vue";
import Hero from "/components/Hero.vue";
import Projects from "/components/Projects.vue";
import Blogs from "/components/Blogs.vue";

const name = "Akith Chandinu";
</script>
