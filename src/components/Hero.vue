<script setup>
import { ArrowRight, Code2, Database, Server, Shield } from "lucide-vue-next";

const floatingBadges = [
  { label: "Laravel", delay: 0 },
  { label: "PHP", delay: 0.4 },
  { label: "JavaScript", delay: 0.8 },
  { label: "Vue", delay: 1.2 },
];

const capabilities = [
  { icon: Server, label: "Laravel Development" },
  { icon: Database, label: "REST API Development" },
  { icon: Shield, label: "Database Management" },
  { icon: Code2, label: "Full Stack Development" },
];
</script>

<template>
  <section id="home" class="relative min-h-screen overflow-hidden pt-28 pb-16 md:pt-32 md:pb-24">
    <div class="mx-auto max-w-7xl px-5 sm:px-8">
      <div class="grid grid-cols-1 items-center gap-12 lg:grid-cols-12 lg:gap-10">
        <!-- Left content -->
        <div class="lg:col-span-7">
          <div
            class="animate-reveal inline-flex items-center gap-2 rounded-full border border-border bg-card/60 px-4 py-1.5 backdrop-blur-sm"
          >
            <span class="size-1.5 rounded-full bg-copper status-dot" />
            <span class="font-mono text-[11px] uppercase tracking-[0.18em] text-muted-foreground">
              Available for work
            </span>
          </div>

          <p
            class="animate-reveal mt-6 text-lg font-medium text-foreground/75 sm:text-xl delay-100"
          >
            Hi, I'm <span class="font-semibold text-copper">Hamza</span>
          </p>

          <h1
            class="animate-reveal heading-xl mt-3 text-4xl text-balance text-foreground sm:text-5xl md:text-6xl delay-200"
          >
            Junior Web <span class="text-copper">Developer</span>
          </h1>

          <p
            class="animate-reveal mt-6 max-w-2xl text-balance text-base leading-relaxed text-muted-foreground sm:text-lg delay-300"
          >
            I build modern, responsive and scalable web applications with Laravel and PHP backends,
            REST APIs and database-driven applications, paired with polished frontend experiences
            using Vue and modern frontend technologies.
          </p>

          <div class="animate-reveal mt-8 flex flex-wrap items-center gap-4 delay-400">
            <a
              href="#projects"
              class="group inline-flex items-center gap-2 rounded-xl bg-primary px-6 py-3 text-sm font-medium text-primary-foreground transition-all hover:bg-primary/90 hover:gap-3"
            >
              View My Work
              <ArrowRight
                :size="16"
                class="transition-transform group-hover:translate-x-0.5"
              />
            </a>
            <a
              href="#contact"
              class="inline-flex items-center rounded-xl border border-border bg-card/50 px-6 py-3 text-sm font-medium text-foreground transition-all hover:border-copper/50 hover:bg-card"
            >
              Let's Talk
            </a>
          </div>

          <!-- Floating badges -->
          <div class="animate-reveal mt-10 flex flex-wrap gap-2 delay-500">
            <span
              v-for="badge in floatingBadges"
              :key="badge.label"
              class="animate-float rounded-lg border border-border bg-card/60 px-3 py-1.5 font-mono text-xs text-muted-foreground backdrop-blur-sm"
              :style="{ animationDelay: `${badge.delay}s` }"
            >
              {{ badge.label }}
            </span>
          </div>
        </div>

        <!-- Right: Profile image frame -->
        <div class="animate-reveal relative lg:col-span-5 delay-200">
          <div
            class="absolute -inset-4 rounded-[2rem] bg-gradient-to-br from-copper/20 via-transparent to-glow/20 blur-2xl"
          />

          <div class="card-surface relative overflow-hidden p-3 glow-soft">
            <div class="relative aspect-[4/5] w-full overflow-hidden rounded-2xl bg-ink-2">
              <img
                src="/hamza.jpg"
                fetchpriority="high"
                alt="Hamza — Full Stack Web Developer"
                class="size-full object-cover object-center"
              />
              <div class="absolute inset-0 bg-gradient-to-t from-ink/60 via-transparent to-transparent" />

              <div class="absolute bottom-4 left-4 right-4">
                <div
                  class="flex items-center gap-3 rounded-xl border border-border/60 bg-ink/70 p-3 backdrop-blur-md"
                >
                  <div class="grid size-10 place-items-center rounded-lg bg-copper/20 text-copper">
                    <Code2 :size="20" />
                  </div>
                  <div>
                    <p class="font-display text-sm font-medium text-foreground">Hamza</p>
                    <p class="font-mono text-[10px] uppercase tracking-widest text-muted-foreground">
                      Junior Web Developer
                    </p>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Floating metadata chips -->
          <div
            class="absolute -bottom-4 -left-4 hidden rounded-xl border border-border bg-card/90 p-3 backdrop-blur-md sm:block"
          >
            <p class="font-mono text-[10px] uppercase tracking-widest text-muted-foreground">Focus</p>
            <p class="font-display text-sm font-semibold text-copper">Backend First</p>
          </div>

          <div
            class="absolute -right-4 top-8 hidden rounded-xl border border-border bg-card/90 p-3 backdrop-blur-md sm:block"
          >
            <p class="font-mono text-[10px] uppercase tracking-widest text-muted-foreground">Stack</p>
            <p class="font-display text-sm font-semibold text-foreground">Laravel · Vue</p>
          </div>
        </div>
      </div>

      <!-- Capability pills -->
      <div class="animate-reveal mt-16 grid gap-4 sm:grid-cols-2 lg:grid-cols-4 delay-500">
        <div
          v-for="item in capabilities"
          :key="item.label"
          class="group flex items-center gap-3 rounded-xl border border-border bg-card/40 p-4 transition-all hover:border-copper/40 hover:bg-card"
        >
          <div
            class="grid size-10 place-items-center rounded-lg bg-secondary text-copper transition-colors group-hover:bg-copper/20"
          >
            <component :is="item.icon" :size="20" />
          </div>
          <span class="font-display text-sm font-medium text-foreground">{{ item.label }}</span>
        </div>
      </div>
    </div>
  </section>
</template>
