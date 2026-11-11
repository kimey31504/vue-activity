<script setup>
    import Navbar from './components/Navbar.vue';
    import Hero from './components/Hero.vue';
    import Projects from './components/Projects.vue';
    import Tools from './components/Tools.vue';
    import Contact from './components/Contact.vue';
    import Footer from './components/Footer.vue';
</script>

<template>
    <Navbar />
    <div class="container-fluid">
        <Hero />
        <Projects />
        <Tools />
        <Contact />
    </div>
    <Footer />
</template>
