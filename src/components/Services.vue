<script setup>
import Reveal from "./Reveal.vue";
import {
  Server,
  Code,
  Network,
  Layers,
  Database,
  Plug,
  LayoutDashboard,
} from "lucide-vue-next";

const services = [
  {
    icon: Server,
    title: "Laravel Development",
    description:
      "Custom web applications, business logic, admin panels, and backend systems built with Laravel.",
  },
  {
    icon: Code,
    title: "PHP Backend Development",
    description:
      "Reliable server-side applications, business logic, and backend functionality built with PHP.",
  },
  {
    icon: Network,
    title: "REST API Development",
    description:
      "Clean, versioned, and documented APIs that power web and mobile applications.",
  },
  {
    icon: Layers,
    title: "Full Stack Web Development",
    description:
      "End-to-end product delivery from database design to responsive user interfaces.",
  },
  {
    icon: Database,
    title: "Database Development",
    description: "Schema design, query optimization, migrations, and Eloquent ORM modeling.",
  },
  {
    icon: Plug,
    title: "Third-Party API Integration",
    description:
      "Payment gateways, email services, social logins, and external platform integrations.",
  },
  {
    icon: LayoutDashboard,
    title: "Admin Dashboard Development",
    description:
      "Building admin dashboards to manage data, role-based access, and reporting features.",
  },
];
</script>

<template>
  <section id="services" class="relative py-24 md:py-32">
    <div class="relative mx-auto max-w-7xl px-5 sm:px-8">
      <!-- Badge centred at the top with About/Skills-style UI; hidden twin below keeps the original spacing so content never moves -->
      <div class="absolute left-1/2 top-1 -translate-x-1/2">
        <Reveal>
          <span class="flex items-center justify-center gap-3 sm:gap-4">
            <span class="h-px w-8 bg-copper/40 sm:w-12"></span>
            <span
              class="whitespace-nowrap font-display text-sm font-medium uppercase tracking-[0.16em] text-copper sm:text-base"
            >
              Services
            </span>
            <span class="h-px w-8 bg-copper/40 sm:w-12"></span>
          </span>
        </Reveal>
      </div>

      <div class="mb-12 max-w-2xl">
        <Reveal>
          <span class="section-label invisible" aria-hidden="true">( 05 ) — Services</span>
        </Reveal>
        <Reveal :delay="100">
          <h2 class="heading-lg mt-4 text-3xl text-balance text-foreground sm:text-4xl">
            What I can build for you
          </h2>
        </Reveal>
        <Reveal :delay="200">
          <p class="mt-4 text-balance text-muted-foreground">
            From backend development to modern frontend experiences, I build reliable web
            solutions tailored to real-world needs.
          </p>
        </Reveal>
      </div>

      <div class="grid gap-5 sm:grid-cols-2 lg:grid-cols-3">
        <Reveal v-for="(service, index) in services" :key="service.title" :delay="index * 80">
          <div
            class="group h-full rounded-2xl border border-border bg-card/40 p-6 transition-all hover:-translate-y-1 hover:border-copper/40 hover:bg-card"
          >
            <div
              class="grid size-12 place-items-center rounded-xl bg-secondary text-copper transition-colors group-hover:bg-copper/20"
            >
              <component :is="service.icon" :size="24" />
            </div>
            <h3 class="mt-5 font-display text-lg font-medium text-foreground">
              {{ service.title }}
            </h3>
            <p class="mt-2 text-sm leading-relaxed text-muted-foreground">
              {{ service.description }}
            </p>
          </div>
        </Reveal>
      </div>
    </div>
  </section>
</template>
