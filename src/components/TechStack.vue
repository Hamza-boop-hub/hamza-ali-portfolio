<script setup>
import Reveal from "./Reveal.vue";

const technologies = [
  { name: "PHP", category: "Backend" },
  { name: "Laravel", category: "Backend" },
  { name: "JavaScript", category: "Frontend" },
  { name: "Vue.js", category: "Frontend" },
  { name: "Node.js", category: "Backend" },
  { name: "MySQL", category: "Database" },
  { name: "Git", category: "Tools" },
  { name: "GitHub", category: "Tools" },
  { name: "Tailwind CSS", category: "Frontend" },
];
</script>

<template>
  <section class="relative py-24 md:py-32">
    <div class="relative mx-auto max-w-7xl px-5 sm:px-8">
      <!-- Badge centred at the top with About/Skills-style UI; hidden twin below keeps the original spacing so content never moves -->
      <div class="absolute left-1/2 top-1 -translate-x-1/2">
        <Reveal>
          <span class="flex items-center justify-center gap-3 sm:gap-4">
            <span class="h-px w-8 bg-copper/40 sm:w-12"></span>
            <span
              class="whitespace-nowrap font-display text-sm font-medium uppercase tracking-[0.16em] text-copper sm:text-base"
            >
              Tech Stack
            </span>
            <span class="h-px w-8 bg-copper/40 sm:w-12"></span>
          </span>
        </Reveal>
      </div>

      <div class="mb-12 text-center">
        <Reveal>
          <span class="section-label invisible" aria-hidden="true">( 06 ) — Tech Stack</span>
        </Reveal>
        <Reveal :delay="100">
          <h2 class="heading-lg mt-4 text-3xl text-balance text-foreground sm:text-4xl">
            Tools of the trade
          </h2>
        </Reveal>
        <Reveal :delay="200">
          <p class="mx-auto mt-4 max-w-xl text-balance text-muted-foreground">
            The core technologies I use to design, build, and ship web applications.
          </p>
        </Reveal>
      </div>

      <div class="flex flex-wrap justify-center gap-3">
        <Reveal v-for="(tech, index) in technologies" :key="tech.name" :delay="index * 40">
          <div
            class="group rounded-xl border border-border bg-card/40 px-5 py-3 transition-all hover:-translate-y-1 hover:border-copper/40 hover:bg-card"
          >
            <span
              class="font-display text-sm font-medium text-foreground transition-colors group-hover:text-copper"
            >
              {{ tech.name }}
            </span>
            <span class="ml-2 font-mono text-[10px] uppercase tracking-wider text-muted-foreground">
              {{ tech.category }}
            </span>
          </div>
        </Reveal>
      </div>
    </div>
  </section>
</template>
