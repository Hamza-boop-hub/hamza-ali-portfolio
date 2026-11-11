<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";
import { Menu, X, Sun, Moon } from "lucide-vue-next";

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

// Theme toggle — applies the real .dark switch on <html> and remembers it
const isDark = ref(true);
const applyTheme = (dark) => {
  isDark.value = dark;
  document.documentElement.classList.toggle("dark", dark);
  try {
    localStorage.setItem("theme", dark ? "dark" : "light");
  } catch {
    /* storage blocked — theme still applies for this page view */
  }
};
const toggleTheme = () => {
  const next = !isDark.value;
  const apply = () => applyTheme(next);

  // 3D flip between themes (View Transition API); instant swap on
  // browsers that don't support it.
  if (typeof document.startViewTransition !== "function") return apply();

  let applied = false;
  const run = () => {
    if (applied) return;
    applied = true;
    apply();
  };
  try {
    const transition = document.startViewTransition(run);
    // A skipped transition (hidden/occluded page) rejects — ignore it; the
    // fallback below still applies the theme.
    transition.ready?.catch(() => {});
  } catch {
    return apply();
  }
  // Safety net: if the page isn't rendering frames the transition never runs
  // its callback — apply directly (idempotent; no-op when `run` already did).
  setTimeout(() => {
    if (!applied) {
      applied = true;
      apply();
    }
  }, 150);
};

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
  // Sync with the theme the pre-paint script already applied
  isDark.value = document.documentElement.classList.contains("dark");
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
        ? 'border-b border-border/60 bg-background/85 shadow-[0_8px_30px_-16px_rgba(15,23,42,0.35)] backdrop-blur-xl'
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
        <span
          class="grid size-8 place-items-center rounded-lg bg-foreground text-sm font-semibold text-background dark:bg-primary dark:text-primary-foreground"
        >
          H
        </span>
        Hamza
      </a>

      <div class="nav-group hidden items-center gap-8 lg:flex">
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

      <div class="flex items-center gap-2.5">
        <a
          href="#contact"
          class="nav-cta hidden rounded-xl bg-primary px-5 py-2.5 text-xs font-medium text-primary-foreground transition-all hover:bg-primary/90 lg:inline-flex"
        >
          Let's Talk
        </a>

        <!-- Dark/light theme toggle — segmented pill (copper sliding knob, springy hover) -->
        <button
          class="theme-pill group relative flex h-10 cursor-pointer items-center rounded-full border border-border bg-secondary/50 p-1 transition-all duration-300 ease-[cubic-bezier(.34,1.56,.64,1)] hover:scale-105 hover:border-primary hover:bg-secondary/80 hover:shadow-[0_0_22px_rgba(232,145,50,0.35)]"
          :aria-label="isDark ? 'Switch to light theme' : 'Switch to dark theme'"
          :title="isDark ? 'Switch to light theme' : 'Switch to dark theme'"
          @click="toggleTheme"
        >
          <span
            class="absolute left-1 top-1 size-8 rounded-full bg-primary shadow-[0_0_10px_rgba(232,145,50,0.4)] transition-transform duration-[400ms] ease-[cubic-bezier(.34,1.56,.64,1)] group-hover:scale-110"
            :class="isDark ? 'translate-x-8' : 'translate-x-0'"
          ></span>
          <span
            class="relative z-10 grid size-8 place-items-center"
            :class="isDark ? 'ico-off' : 'text-primary-foreground'"
          >
            <Sun :size="15" class="sun-ico" />
          </span>
          <span
            class="relative z-10 grid size-8 place-items-center"
            :class="isDark ? 'text-primary-foreground' : 'ico-off'"
          >
            <Moon :size="15" class="moon-ico" />
          </span>
        </button>

        <button
          class="grid size-10 cursor-pointer place-items-center rounded-lg border border-border text-foreground lg:hidden"
          :aria-label="isOpen ? 'Close menu' : 'Open menu'"
          @click="isOpen = !isOpen"
        >
          <X v-if="isOpen" :size="20" />
          <Menu v-else :size="20" />
        </button>
      </div>
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
          class="rounded-lg px-3 py-3 text-sm font-medium normal-case tracking-normal transition-colors hover:bg-secondary hover:text-foreground dark:font-mono dark:font-normal dark:uppercase dark:tracking-[0.1em]"
          :class="
            activeSection === link.href.slice(1)
              ? 'bg-copper/15 text-primary dark:bg-transparent'
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

<style scoped>
/* Theme pill — lively hover feel: spinny sun, rocking moon, brightening off-icon */
.theme-pill .ico-off {
  color: var(--muted-foreground);
  transition: color 0.3s ease;
}
.theme-pill:hover .ico-off {
  color: var(--foreground);
}
.theme-pill:hover .sun-ico {
  animation: pill-spin 6s linear infinite;
}
.theme-pill:hover .moon-ico {
  animation: pill-rock 1.8s ease-in-out infinite;
}

@keyframes pill-spin {
  to {
    transform: rotate(360deg);
  }
}
@keyframes pill-rock {
  0%,
  100% {
    transform: rotate(0deg);
  }
  25% {
    transform: rotate(15deg);
  }
  75% {
    transform: rotate(-15deg);
  }
}

@media (prefers-reduced-motion: reduce) {
  .theme-pill:hover .sun-ico,
  .theme-pill:hover .moon-ico {
    animation: none;
  }
}
</style>
