<script setup>
import Reveal from "./Reveal.vue";
import { Server, Database, Shield, Plug, Layout, FileCode } from "lucide-vue-next";

const highlights = [
  {
    icon: Server,
    title: "Backend Development",
    description:
      "Building robust server-side applications with Laravel and PHP, from custom business logic to scalable API architectures.",
  },
  {
    icon: Database,
    title: "Database Design",
    description:
      "Designing normalized schemas, writing optimized queries, and modeling data with Eloquent ORM for maintainable applications.",
  },
  {
    icon: Shield,
    title: "Authentication & Authorization",
    description:
      "Implementing secure login systems, role-based access control, and protected API endpoints using Laravel Sanctum and Gates.",
  },
  {
    icon: Plug,
    title: "Third-Party API Integration",
    description:
      "Connecting applications with payment gateways, email services, maps, and external platforms through clean REST integrations.",
  },
  {
    icon: Layout,
    title: "Frontend Integration",
    description:
      "Crafting responsive, accessible interfaces with Vue.js, Tailwind CSS, and modern JavaScript patterns.",
  },
  {
    icon: FileCode,
    title: "Clean, Maintainable Code",
    description:
      "Writing clean, structured, and reusable code that is easy to test, maintain, and scale.",
  },
];
</script>

<template>
  <section id="about" class="relative py-24 md:py-32">
    <div class="mx-auto max-w-7xl px-5 sm:px-8">
      <div class="grid grid-cols-1 gap-12 lg:grid-cols-12 lg:gap-16">
        <div class="lg:col-span-5">
          <div class="flex gap-4 sm:gap-5">
            <div class="flex shrink-0 flex-col items-center gap-4 pt-1">
              <Reveal>
                <span
                  class="font-display rotate-180 whitespace-nowrap text-sm font-medium uppercase tracking-[0.16em] text-copper [writing-mode:vertical-rl] sm:text-base"
                >
                  About Me
                </span>
              </Reveal>
              <span class="h-24 w-px bg-copper/40 sm:h-32"></span>
            </div>
            <div class="min-w-0 flex-1">
              <Reveal :delay="100">
                <h2 class="heading-lg text-3xl text-balance text-foreground sm:text-4xl">
                  Need a <em class="italic text-copper">Creative</em> Project? I can Help You!
                </h2>
              </Reveal>
              <Reveal :delay="200">
                <p class="mt-6 text-balance leading-relaxed text-muted-foreground">
                  I'm Hamza, a PHP &amp; Laravel developer who enjoys turning ideas into real,
                  working websites. From clean backend architecture to a smooth, modern frontend, I
                  build complete, database-driven products — REST APIs, dashboards, and interfaces
                  that hold up in production.
                </p>
              </Reveal>
              <Reveal :delay="300">
                <p class="mt-4 text-balance leading-relaxed text-muted-foreground">
                  I care about writing code that's easy to maintain, not just code that works
                  today. Every project I take on gets the same attention to detail, from planning
                  the structure to the final deployment.
                </p>
              </Reveal>
              <Reveal :delay="400">
                <p class="mt-4 text-balance leading-relaxed text-muted-foreground">
                  When I'm not coding, I'm usually exploring new tools and techniques to keep
                  improving how I build things.
                </p>
              </Reveal>
            </div>
          </div>
        </div>

        <div class="lg:col-span-7">
          <div class="grid gap-4 sm:grid-cols-2">
            <Reveal v-for="(item, index) in highlights" :key="item.title" :delay="index * 80">
              <div
                class="group h-full rounded-2xl border border-border bg-card/40 p-6 transition-all hover:border-copper/40 hover:bg-card"
              >
                <div
                  class="grid size-11 place-items-center rounded-xl bg-secondary text-copper transition-colors group-hover:bg-copper/20"
                >
                  <component :is="item.icon" :size="22" />
                </div>
                <h3 class="mt-4 font-display text-lg font-medium text-foreground">
                  {{ item.title }}
                </h3>
                <p class="mt-2 text-sm leading-relaxed text-muted-foreground">
                  {{ item.description }}
                </p>
              </div>
            </Reveal>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>
