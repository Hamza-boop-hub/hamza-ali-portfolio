<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";
import { Menu, X } from "lucide-vue-next";

const navLinks = [
  { label: "Home", href: "#home" },
  { label: "About", href: "#about" },
  { label: "Skills", href: "#skills" },
  { label: "Experience", href: "#experience" },
  { label: "Projects", href: "#projects" },
  { label: "Services", href: "#services" },
  { label: "Contact", href: "#contact" },
];

const isOpen = ref(false);
const scrolled = ref(false);
const activeSection = ref("");

const replayIntro = () => {
  if (typeof window.__replayPreloader === "function") window.__replayPreloader();
};

// Scroll-spy: highlight the section crossing the viewport's middle band (45%-50%).
// Re-queries sections on every scroll so it survives HMR-replaced nodes and
// late-mounted sections (the old one-shot IntersectionObserver went stale).
const updateActiveSection = () => {
  const sections = document.querySelectorAll("section[id]");
  const bandY = window.innerHeight * 0.45;
  let current = "";
  sections.forEach((section) => {
    const rect = section.getBoundingClientRect();
    if (rect.top <= bandY && rect.bottom > bandY) current = section.id;
  });
  if (current) activeSection.value = current;
};

const handleScroll = () => {
  scrolled.value = window.scrollY > 20;
  updateActiveSection();
};

onMounted(() => {
  window.addEventListener("scroll", handleScroll, { passive: true });
  handleScroll();
});

onBeforeUnmount(() => {
  window.removeEventListener("scroll", handleScroll);
});
</script>

<template>
  <header
    class="fixed left-0 right-0 top-0 z-50 transition-all duration-300"
    :class="
      scrolled
        ? 'border-b border-border/60 bg-background/85 backdrop-blur-xl'
        : 'bg-transparent'
    "
  >
    <nav class="mx-auto flex max-w-7xl items-center justify-between px-5 py-4 sm:px-8">
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

      <div class="hidden items-center gap-8 lg:flex">
        <a
          v-for="link in navLinks"
          :key="link.href"
          :href="link.href"
          class="nav-link font-mono text-xs uppercase tracking-[0.15em] transition-colors hover:text-primary"
          :class="
            activeSection === link.href.slice(1)
              ? 'is-active text-primary'
              : 'text-muted-foreground'
          "
        >
          {{ link.label }}
        </a>
      </div>

      <a
        href="#contact"
        class="hidden rounded-xl bg-primary px-5 py-2.5 text-xs font-medium text-primary-foreground transition-all hover:bg-primary/90 lg:inline-flex"
      >
        Let's Talk
      </a>

      <button
        class="grid size-10 place-items-center rounded-lg border border-border text-foreground lg:hidden"
        :aria-label="isOpen ? 'Close menu' : 'Open menu'"
        @click="isOpen = !isOpen"
      >
        <X v-if="isOpen" :size="20" />
        <Menu v-else :size="20" />
      </button>
    </nav>

    <!-- Mobile menu -->
    <div
      class="fixed inset-x-0 top-[72px] z-40 overflow-hidden border-b border-border/60 bg-background transition-all duration-300 lg:hidden"
      :class="isOpen ? 'max-h-[calc(100vh-72px)] opacity-100' : 'max-h-0 opacity-0'"
    >
      <div class="flex h-[calc(100vh-72px)] flex-col gap-1 px-5 py-6">
        <a
          v-for="link in navLinks"
          :key="link.href"
          :href="link.href"
          class="rounded-lg px-3 py-3 font-mono text-sm uppercase tracking-[0.1em] transition-colors hover:bg-secondary hover:text-foreground"
          :class="
            activeSection === link.href.slice(1)
              ? 'text-primary'
              : 'text-muted-foreground'
          "
          @click="isOpen = false"
        >
          {{ link.label }}
        </a>
        <a
          href="#contact"
          class="mt-4 rounded-xl bg-primary px-3 py-3 text-center text-sm font-medium text-primary-foreground"
          @click="isOpen = false"
        >
          Let's Talk
        </a>
      </div>
    </div>
  </header>
</template>
