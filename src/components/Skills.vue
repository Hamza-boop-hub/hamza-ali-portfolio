<script setup>
import Reveal from "./Reveal.vue";
import { Terminal, Repeat, Sparkles } from "lucide-vue-next";
import { brandTechnologies } from "../data/skillsTechnologies.js";

const byName = (n) => brandTechnologies.find((b) => b.name === n);

// Same skills — ordered backend-first (user's focus), Laravel leads.
const technologies = [
  // Backend
  byName("Laravel"),
  byName("PHP"),
  // Database
  byName("MySQL"),
  // Backend-adjacent tools
  { name: "REST APIs", icon: Repeat, hex: "#38BDF8" },
  { name: "Command Line", icon: Terminal, hex: "#E7E5E4" },
  // Frontend
  byName("JavaScript"),
  byName("Vue.js"),
  byName("HTML5"),
  // CSS shield's brand purple (#663399) is too dark for a dark tile — use a lighter violet.
  { ...byName("CSS"), hex: "#8B5CF6" },
  byName("Tailwind CSS"),
  byName("Bootstrap"),
  // Tools
  byName("GitHub"),
  byName("Cursor"),
  { name: "ChatGPT", icon: Sparkles, hex: "#E7E5E4" },
];

// Brand logos that are near-black would vanish on a dark tile — render those in a light neutral.
const fillFor = (hex) => {
  const c = hex.replace("#", "");
  const r = parseInt(c.slice(0, 2), 16);
  const g = parseInt(c.slice(2, 4), 16);
  const b = parseInt(c.slice(4, 6), 16);
  const lum = (0.299 * r + 0.587 * g + 0.114 * b) / 255;
  return lum < 0.32 ? "#E7E5E4" : hex;
};
</script>

<template>
  <section id="skills" class="relative py-24 md:py-32">
    <div class="relative mx-auto max-w-7xl px-5 sm:px-8">
      <!-- Section badge centred at the top; invisible twin keeps the site-wide spacing pattern -->
      <div class="absolute left-1/2 top-1 -translate-x-1/2">
        <Reveal>
          <span class="flex items-center justify-center gap-3 sm:gap-4">
            <span class="h-px w-8 bg-copper/40 sm:w-12"></span>
            <span
              class="whitespace-nowrap font-display text-sm font-medium uppercase tracking-[0.16em] text-copper sm:text-base"
            >
              My Skills
            </span>
            <span class="h-px w-8 bg-copper/40 sm:w-12"></span>
          </span>
        </Reveal>
      </div>

      <div class="text-center">
        <Reveal>
          <span class="section-label invisible" aria-hidden="true">My Skills</span>
        </Reveal>
        <Reveal :delay="100">
          <h2 class="heading-lg mt-4 text-3xl text-balance text-foreground sm:text-5xl">
            Technologies I build with
          </h2>
        </Reveal>
      </div>

      <div class="mt-10 grid grid-cols-2 gap-3 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-6">
        <Reveal
          v-for="(tech, index) in technologies"
          :key="tech.name"
          :delay="index * 40"
          class="h-full"
        >
          <div
            class="group flex h-full flex-col items-center gap-3 rounded-2xl border border-border bg-card/40 px-3 py-5 transition-all hover:-translate-y-1 hover:border-copper hover:bg-card"
          >
            <!-- Tile: outlined by default, solid copper on hover (logo flips to white) -->
            <span
              class="grid size-11 place-items-center rounded-xl border border-border bg-secondary/40 transition-all group-hover:border-copper group-hover:bg-copper"
            >
              <svg
                v-if="tech.path"
                viewBox="0 0 24 24"
                class="size-6 transition-all group-hover:brightness-0 group-hover:invert"
                aria-hidden="true"
              >
                <path :d="tech.path" :fill="fillFor(tech.hex)" />
              </svg>
              <component
                :is="tech.icon"
                v-else
                :size="22"
                :style="{ color: fillFor(tech.hex) }"
                class="transition-all group-hover:brightness-0 group-hover:invert"
                aria-hidden="true"
              />
            </span>
            <span
              class="text-center text-xs text-muted-foreground transition-colors group-hover:text-foreground"
            >
              {{ tech.name }}
            </span>
          </div>
        </Reveal>
      </div>
    </div>
  </section>
</template>
