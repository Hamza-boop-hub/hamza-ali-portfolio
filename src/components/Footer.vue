<script setup>
import { Github, Linkedin, Mail } from "lucide-vue-next";

const footerLinks = [
  { label: "Home", href: "#home" },
  { label: "About", href: "#about" },
  { label: "Skills", href: "#skills" },
  { label: "Experience", href: "#experience" },
  { label: "Projects", href: "#projects" },
  { label: "Services", href: "#services" },
  { label: "Contact", href: "#contact" },
];

const socialLinks = [
  { icon: Github, href: "https://github.com/Hamza-boop-hub", label: "GitHub" },
  { icon: Linkedin, href: "https://www.linkedin.com/in/hazma-ali-965101429/", label: "LinkedIn" },
  { icon: Mail, href: "https://mail.google.com/mail/?view=cm&fs=1&to=hamzamumtaz0600@gmail.com", label: "Email" },
];

const currentYear = new Date().getFullYear();

const replayIntro = () => {
  if (typeof window.__replayPreloader === "function") window.__replayPreloader();
};
</script>

<template>
  <footer class="border-t border-border bg-ink/50 py-12">
    <div class="mx-auto max-w-7xl px-5 sm:px-8">
      <div class="flex flex-col items-start justify-between gap-8 lg:flex-row lg:items-center">
        <div>
          <a
            href="#home"
            class="flex items-center gap-2.5 font-display text-lg font-semibold tracking-tight text-foreground"
            title="Replay intro"
            @click.prevent="replayIntro"
          >
            <span class="grid size-8 place-items-center rounded-lg bg-primary text-sm font-semibold text-primary-foreground">
              H
            </span>
            Hamza
          </a>
          <p class="mt-2 text-sm text-muted-foreground">
            Junior Web Developer
          </p>
        </div>

        <nav class="flex flex-wrap gap-x-6 gap-y-2">
          <a
            v-for="link in footerLinks"
            :key="link.href"
            :href="link.href"
            class="font-mono text-xs uppercase tracking-[0.12em] text-muted-foreground transition-colors hover:text-primary"
          >
            {{ link.label }}
          </a>
        </nav>

        <div class="flex items-center gap-4">
          <a
            v-for="social in socialLinks"
            :key="social.label"
            :href="social.href"
            :target="social.href.startsWith('http') ? '_blank' : null"
            :rel="social.href.startsWith('http') ? 'noopener noreferrer' : null"
            :aria-label="social.label"
            class="grid size-10 place-items-center rounded-lg border border-border text-muted-foreground transition-all hover:border-copper/40 hover:text-copper"
          >
            <component :is="social.icon" :size="18" />
          </a>
        </div>
      </div>

      <div
        class="mt-10 flex flex-col items-center justify-between gap-3 border-t border-border/60 pt-6 sm:flex-row"
      >
        <p class="font-mono text-xs text-muted-foreground">
          © {{ currentYear }} Hamza. All rights reserved.
        </p>
        <p class="font-mono text-xs text-muted-foreground/70">
          Built with Vue 3 &amp; Tailwind CSS
        </p>
      </div>
    </div>
  </footer>
</template>
