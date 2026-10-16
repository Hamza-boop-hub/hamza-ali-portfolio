<script setup>
import Reveal from "./Reveal.vue";

const experiences = [
  {
    period: "Self-Learning",
    role: "Self-Taught Web Developer",
    company: "Independent Learning",
    description:
      "I learned HTML, CSS, JavaScript, and Laravel through self-study and hands-on practice, building a strong foundation in web development.",
  },
  {
    period: "3 Months",
    role: "Web Development Intern",
    company: "Sublime Logics",
    description:
      "Completed a three-month internship at Sublime Logics, where I gained hands-on experience in web development, learned professional coding practices, and contributed to development tasks.",
  },
  {
    period: "6 Months",
    role: "Junior Web Developer",
    company: "Sublime Logics",
    description:
      "Gained six months of practical experience at Sublime Logics, working on web development tasks, contributing to real-world projects, and improving my development skills.",
  },
];
</script>

<template>
  <section id="experience" class="relative py-24 md:py-32">
    <div class="relative mx-auto max-w-7xl px-5 sm:px-8">
      <!-- Badge centred at the top with About/Skills-style UI; hidden twin below keeps the original spacing so content never moves -->
      <div class="absolute left-1/2 top-1 -translate-x-1/2">
        <Reveal>
          <span class="flex items-center justify-center gap-3 sm:gap-4">
            <span class="h-px w-8 bg-copper/40 sm:w-12"></span>
            <span
              class="whitespace-nowrap font-display text-sm font-medium uppercase tracking-[0.16em] text-copper sm:text-base"
            >
              Experience
            </span>
            <span class="h-px w-8 bg-copper/40 sm:w-12"></span>
          </span>
        </Reveal>
      </div>

      <div class="mb-12 max-w-2xl">
        <Reveal>
          <span class="section-label invisible" aria-hidden="true">( 03 ) — Experience</span>
        </Reveal>
        <Reveal :delay="100">
          <h2 class="heading-lg mt-4 text-3xl text-balance text-foreground sm:text-4xl">
            My development journey
          </h2>
        </Reveal>
        <Reveal :delay="200">
          <p class="mt-4 text-balance text-muted-foreground">
            My journey from learning web development independently to gaining hands-on industry
            experience.
          </p>
        </Reveal>
      </div>

      <!-- Minimal editorial list + left timeline line with copper dots -->
      <div class="relative max-w-5xl border-t border-border">
        <!-- Timeline line (fades at both ends so it never hard-joins the border rules) -->
        <div
          class="absolute left-0 top-4 bottom-4 w-px bg-gradient-to-b from-transparent via-border to-transparent"
        />

        <div
          v-for="(exp, index) in experiences"
          :key="index"
          class="relative grid gap-3 border-b border-border py-8 pl-10 md:grid-cols-[180px_1fr] md:gap-10 md:py-10"
        >
          <!-- Timeline dot (aligned with the period text) -->
          <div
            class="absolute left-0 top-[38px] z-10 size-3 -translate-x-1/2 rounded-full border-2 border-background bg-copper md:top-[46px]"
          />

          <Reveal :delay="index * 120">
            <span class="font-mono text-xs uppercase tracking-widest text-copper">{{ exp.period }}</span>
          </Reveal>
          <Reveal :delay="index * 120 + 80">
            <div>
              <h3 class="font-display text-lg font-medium text-foreground">
                {{ exp.role }}
              </h3>
              <p class="mt-1 text-sm italic text-muted-foreground">
                {{ exp.company }}
              </p>
              <p class="mt-3 max-w-3xl text-sm leading-relaxed text-muted-foreground/80">
                {{ exp.description }}
              </p>
            </div>
          </Reveal>
        </div>
      </div>
    </div>
  </section>
</template>
