<script setup>
import { ref, computed, watch, onMounted, onUnmounted, nextTick } from "vue";
import Reveal from "./Reveal.vue";
import { ArrowLeft, Braces, Check, CheckCheck, Code, ExternalLink, Github, Layers, Palette, PenTool, X } from "lucide-vue-next";

// Index of the project whose full-screen detail view is open (reference style)
const openProject = ref(null);

// Tech-chip icon by tag (theme-matched lucide glyphs, reference style)
function tagIcon(tag) {
  const k = String(tag).toLowerCase();
  if (k.includes("html")) return Code;
  if (k.includes("css")) return Palette;
  if (k.includes("script")) return Braces;
  if (k.includes("design") || k.includes("vue") || k.includes("tailwind")) return PenTool;
  return Layers;
}

// --- Draggable screenshot gallery: grab cursor, drag to move, infinite loop ---
// The track renders a clone of the LAST slide at index 0 and a clone of the FIRST
// after the real ones, so dragging past either edge wraps around seamlessly.
const galleryIndex = ref(1);
const galleryDragX = ref(0);
const galleryDragging = ref(false);
const galleryNoAnim = ref(false); // transition off for the silent loop-jump frame
const galleryLocked = ref(false); // ignore new drags while a snap/loop-jump runs
let galleryStartX = 0;
let galleryLoopTimer = null;

const galleryImages = computed(() => {
  const p = openProject.value !== null ? projects[openProject.value] : null;
  if (!p) return [];
  return p.gallery && p.gallery.length ? p.gallery : [p.image];
});
const galleryCanDrag = computed(() => galleryImages.value.length > 1);

// Slides in the track — clones only when there is more than one image
const gallerySlides = computed(() => {
  const imgs = galleryImages.value;
  return imgs.length > 1 ? [imgs[imgs.length - 1], ...imgs, imgs[0]] : imgs;
});

function galleryDown(e) {
  if (!galleryCanDrag.value || galleryLocked.value) return;
  galleryAutoStop(); // pause the auto slider while the user drags
  galleryDragging.value = true;
  galleryStartX = e.clientX;
  try {
    e.currentTarget.setPointerCapture(e.pointerId);
  } catch (err) {
    /* pointer capture not available — drag still works via move/up on element */
  }
}
function galleryMove(e) {
  if (!galleryDragging.value) return;
  // free drag (no dead ends) — clamped to one slide width so only the neighbour peeks in
  const w = e.currentTarget.clientWidth || 1;
  let dx = e.clientX - galleryStartX;
  dx = Math.max(-w, Math.min(w, dx));
  galleryDragX.value = dx;
}
// Let the 400ms snap slide finish; if we landed on a clone, jump to the real
// slide with the transition off — the images are identical, so it's invisible.
function galleryLockAndWrap(n) {
  galleryLocked.value = true;
  clearTimeout(galleryLoopTimer);
  galleryLoopTimer = setTimeout(() => {
    if (galleryIndex.value === 0 || galleryIndex.value === n + 1) {
      galleryNoAnim.value = true;
      galleryIndex.value = galleryIndex.value === 0 ? n : 1;
      setTimeout(() => {
        galleryNoAnim.value = false;
        galleryLocked.value = false;
      }, 80);
    } else {
      galleryLocked.value = false;
    }
  }, 430);
}

function galleryUp() {
  if (!galleryDragging.value) return;
  const threshold = 70;
  let advanced = false;
  if (galleryDragX.value <= -threshold) {
    galleryIndex.value += 1;
    advanced = true;
  } else if (galleryDragX.value >= threshold) {
    galleryIndex.value -= 1;
    advanced = true;
  }
  galleryDragging.value = false;
  galleryDragX.value = 0;
  galleryAutoStart(); // (re)start the 3s auto slider after any touch
  if (!advanced) return;
  galleryLockAndWrap(galleryImages.value.length);
}

// --- Auto slider: advance one slide every 3s; paused while the user drags ---
let galleryAutoTimer = null;

function galleryAutoStop() {
  if (galleryAutoTimer) {
    clearInterval(galleryAutoTimer);
    galleryAutoTimer = null;
  }
}
function galleryAutoStart() {
  galleryAutoStop();
  if (!galleryCanDrag.value) return; // single image → no autoplay
  galleryAutoTimer = setInterval(galleryAutoTick, 3000);
}
function galleryAutoTick() {
  galleryGoNext();
  galleryAutoStart(); // keep the strict 3s cadence from this moment
}
function galleryGoNext() {
  if (!galleryCanDrag.value || galleryDragging.value || galleryLocked.value) return;
  galleryIndex.value += 1;
  galleryLockAndWrap(galleryImages.value.length);
}

// Lock body scroll while the detail view is open; reset gallery position.
// A history entry is pushed on open so the browser back button closes the view.
let detailHistoryActive = false;

// Play the branded intro loader as a transition (keeps hash + scroll position)
function playLoader() {
  try {
    if (typeof window.__replayPreloader === "function") {
      window.__replayPreloader({ keepLocation: true });
    }
  } catch (err) {
    /* loader unavailable — close without transition */
  }
}

function closeDetail() {
  if (openProject.value === null) return;
  playLoader(); // covers the screen while the view closes underneath
  openProject.value = null;
  if (detailHistoryActive) {
    detailHistoryActive = false;
    history.back(); // popstate fires — handler no-ops because we already closed
  }
}

// Brand click in the detail bar: branded loader + land on the home page start.
// The pushed detail history entry is popped for real (same as closeDetail) so
// the stack never keeps a stale "home" entry — otherwise a later X-close would
// restore that entry and jump to the home page instead of the projects list.
// NOTE: the loader locks body scroll (body.is-loading), so the top scroll is
// applied the moment it releases — underneath the fading loader.
function goHome() {
  if (openProject.value === null) return;
  playLoader(); // branded loader covers the screen right away (keeps location)
  openProject.value = null;

  const prevRestoration = history.scrollRestoration;
  try {
    history.scrollRestoration = "manual"; // we navigate to the top ourselves
  } catch (err) {
    /* noop */
  }

  let settled = false;
  const finish = () => {
    if (settled) return;
    settled = true;
    try {
      if (location.hash) {
        history.replaceState(null, "", location.pathname + location.search);
      }
    } catch (err2) {
      /* noop */
    }
    const applyTop = () => {
      window.scrollTo({ top: 0, behavior: "instant" });
      try {
        history.scrollRestoration = prevRestoration;
      } catch (err3) {
        /* noop */
      }
    };
    if (!document.body.classList.contains("is-loading")) {
      applyTop();
    } else {
      let applied = false;
      const run = () => {
        if (applied) return;
        applied = true;
        applyTop();
      };
      const iv = setInterval(() => {
        if (!document.body.classList.contains("is-loading")) {
          clearInterval(iv);
          run();
        }
      }, 80);
      setTimeout(run, 4000); // safety cap
    }
  };

  if (detailHistoryActive) {
    detailHistoryActive = false;
    window.addEventListener("popstate", finish, { once: true });
    history.back(); // pop the pushed detail entry — stack stays clean
    setTimeout(finish, 500); // safety in case popstate never arrives
  } else {
    nextTick(finish);
  }
}

// Opening a project plays the same branded loader first (view opens underneath)
function openDetail(index) {
  playLoader();
  openProject.value = index;
}

const onPopState = () => {
  detailHistoryActive = false;
  if (openProject.value !== null) {
    playLoader();
    openProject.value = null;
  }
};

watch(openProject, (val) => {
  document.body.style.overflow = val !== null ? "hidden" : "";
  clearTimeout(galleryLoopTimer);
  galleryAutoStop();
  galleryIndex.value = 1;
  galleryDragX.value = 0;
  galleryDragging.value = false;
  galleryNoAnim.value = false;
  galleryLocked.value = false;
  if (val !== null) galleryAutoStart();
  if (val !== null && !detailHistoryActive) {
    detailHistoryActive = true;
    history.pushState({ projectDetail: true }, "");
  }
});
const onKeydown = (e) => {
  if (e.key === "Escape") closeDetail();
};
onMounted(() => {
  window.addEventListener("keydown", onKeydown);
  window.addEventListener("popstate", onPopState);
});
onUnmounted(() => {
  window.removeEventListener("keydown", onKeydown);
  window.removeEventListener("popstate", onPopState);
  document.body.style.overflow = "";
  galleryAutoStop();
});

const projects = [
  {
    title: "Sweet Creamery",
    headline: "Responsive Ice Cream & Dessert Shop Landing Page",
    description:
      "An ice cream shop website with a responsive layout and modern UI, deployed live on Netlify.",
    overview:
      "hamza-sweetcreamery is a colorful, single-page website for an ice cream and dessert shop. It showcases flavors, promotional deals, customer favorites, the brand story, reviews, and a community gallery through a modern, fully responsive front-end built for desktop, tablet, and mobile.",
    features: [
      "Fully responsive single-page layout for desktop, tablet, and mobile",
      "Fixed navigation bar with active-section highlighting",
      "Animated hero section with product artwork and a customer rating card",
      "Flavor carousel with six product cards",
      "Promotional deals and customer favorites section",
      "Feature cards for ingredients, craftsmanship, delivery, and sustainability",
      "Brand story section with supporting imagery",
      "Customer reviews slider",
      "Community gallery with responsive image cards",
      "Newsletter subscription form",
      "Smooth scrolling and scroll-to-top control",
      "Custom hover effects, transitions, and reduced-motion support",
    ],
    image: "/projects/sweet-creamery-full.jpg",
    pan: "-88.7%",
    gallery: [
      "/projects/sweet-creamery-home.jpg",
      "/projects/sweet-creamery-story.jpg",
      "/projects/sweet-creamery-gallery.jpg",
      "/projects/sweet-creamery-footer.jpg",
    ],
    tags: ["HTML5", "CSS", "JavaScript"],
    github: "https://github.com/Hamza-boop-hub",
    demo: "https://hamza-sweetcreamery.netlify.app/",
  },
  {
    title: "Sublime Logics",
    description:
      "Official company website for Sublime Logics presenting enterprise software, AI agent development, and automation services with case studies and industry focus.",
    overview:
      "sublimelogics.com is the official website for Sublime Logics — an enterprise software, AI, and automation company. It introduces their services, portfolio, industries, and company through a dark, fully responsive design built to turn visitors into enquiries.\n\nI contributed to the website by integrating a chatbot, adding Google reCAPTCHA to forms, connecting contact, job application, and student registration forms with the CRM admin panel, and integrating Microsoft Clarity and Google Tag Manager for website analytics and tracking.",
    features: [
      "Responsive Corporate Website — Modern, responsive design for a consistent experience across devices",
      "AI Chatbot Integration — Integrated a chatbot to help visitors explore website information and get answers to their questions",
      "Secure Form Submissions — Added Google reCAPTCHA to website forms to help prevent spam and automated submissions",
      "CRM Integration — Connected contact, job application, and student registration forms to save submissions in the CRM admin panel",
      "Microsoft Clarity Analytics — Integrated Clarity to analyze visitor behavior through session recordings and heatmaps",
      "Google Tag Manager — Integrated GTM to manage website tracking tags and analytics scripts",
    ],
    image: "/projects/sublimelogics-full.jpg",
    pan: "-85%",
    gallery: [
      "/projects/sublimelogics-hero.jpg",
      "/projects/sublimelogics-services.jpg",
      "/projects/sublimelogics-industries.jpg",
      "/projects/sublimelogics-footer.jpg",
      "/projects/sublimelogics-form-contact.jpg",
      "/projects/sublimelogics-form-job.jpg",
      "/projects/sublimelogics-form-register.jpg",
    ],
    tags: ["Vue.js", "Nuxt", "Tailwind"],
    demo: "https://www.sublimelogics.com/",
  },
  {
    title: "Employee Performance Review System",
    headline: "Monthly Performance Reviews & KPI Scoring for an Accounting App",
    description:
      "A complete performance review module for a company accounting app — automated monthly reviews, an employee self-review portal, weighted KPI scoring, and performance reports.",
    overview:
      "The Employee Performance Review System is a full module built into a Laravel accounting app using Filament v3. On the last Friday of every month it automatically generates a review for each active employee. Employees open a dedicated portal with their employee ID — no login needed — and submit their self-review, while management evaluates them against weighted KPIs to produce a final score out of 100.\n\nReviews move through a clear status workflow — Pending, Submitted, Evaluated, and Completed. Managers rate every active KPI from 1 to 5 with comments while a live provisional score updates in real time; completing the review locks the final score and notifies the employee. A report page tracks reviewed counts, average, highest and lowest scores, plus per-employee 6-month and 12-month averages.",
    features: [
      "Automated Monthly Generation — an artisan command creates one review per active employee on the last Friday of every month",
      "Employee Self-Review Portal — employees look up their reviews with an employee ID and submit answers",
      "Flexible Question Bank — six answer types (text, long text, yes/no, select, checkbox, rating) with drag-to-reorder ordering",
      "Weighted KPI Evaluation — managers rate every active KPI from 1–5 with comments; weights drive the final score out of 100",
      "Live Provisional Score — the weighted score recalculates and displays live while the review is being evaluated",
      "Status Workflow & Notifications — Pending → Submitted → Evaluated → Completed with Discord and email notifications",
      "Performance Reports — month and employee filters with reviewed counts, average/highest/lowest scores, and 6-month & 12-month averages per employee",
      "Answer Snapshots — each review stores a snapshot of its questions and options, so past answers stay intact if the question bank changes",
    ],
    image: "/projects/eprs-full.jpg",
    pan: "-25.3%",
    gallery: [
      "/projects/eprs-portal-home.jpg",
      "/projects/eprs-question-bank.jpg",
      "/projects/eprs-question-edit.jpg",
      "/projects/eprs-kpis.jpg",
      "/projects/eprs-kpi-edit.jpg",
      "/projects/eprs-generate-modal.jpg",
      "/projects/eprs-reviews-pending.jpg",
      "/projects/eprs-portal-list.jpg",
      "/projects/eprs-self-review-empty.jpg",
      "/projects/eprs-self-review.jpg",
      "/projects/eprs-review-submitted.jpg",
      "/projects/eprs-reviews-list.jpg",
      "/projects/eprs-evaluate-empty.jpg",
      "/projects/eprs-kpi-evaluation.jpg",
      "/projects/eprs-complete-modal.jpg",
      "/projects/eprs-review-completed.jpg",
      "/projects/eprs-employee-score.jpg",
      "/projects/eprs-report.jpg",
    ],
    tags: ["Laravel", "Filament", "Livewire", "MySQL"],
  },
];
</script>

<template>
  <section id="projects" class="relative py-24 md:py-32">
    <div class="relative mx-auto max-w-7xl px-5 sm:px-8">
      <!-- Badge centred at the top with About/Skills-style UI; hidden twin below keeps the original spacing so content never moves -->
      <div class="absolute left-1/2 top-1 -translate-x-1/2">
        <Reveal>
          <span class="flex items-center justify-center gap-3 sm:gap-4">
            <span class="h-px w-8 bg-copper/40 sm:w-12"></span>
            <span
              class="whitespace-nowrap font-display text-sm font-medium uppercase tracking-[0.16em] text-copper sm:text-base"
            >
              Projects
            </span>
            <span class="h-px w-8 bg-copper/40 sm:w-12"></span>
          </span>
        </Reveal>
      </div>

      <div class="mb-12 flex flex-col gap-4 sm:flex-row sm:items-end sm:justify-between">
        <div>
          <Reveal>
            <span class="section-label invisible" aria-hidden="true">( 04 ) — Projects</span>
          </Reveal>
          <Reveal :delay="100">
            <h2 class="heading-lg mt-4 text-3xl text-balance text-foreground sm:text-4xl">
              Featured Work
            </h2>
          </Reveal>
        </div>
      </div>

      <div class="grid gap-6 md:grid-cols-2 lg:grid-cols-3">
        <Reveal v-for="(project, index) in projects" :key="project.title" :delay="index * 100">
          <article
            class="group relative cursor-pointer overflow-hidden rounded-2xl border border-border bg-card/40 transition-all hover:-translate-y-1.5 hover:border-copper/40"
            @click="openDetail(index)"
          >
            <!-- Screenshot only: pans through the whole page on hover -->
            <div class="relative aspect-square overflow-hidden bg-ink-2">
              <!-- GPU transform slide: whole image layer translates (no re-crop = no pixel tearing) -->
              <img
                :src="project.image"
                :alt="project.title"
                loading="lazy"
                draggable="false"
                :style="{ '--pan': project.pan }"
                class="absolute inset-x-0 top-0 w-full max-w-none transition-transform duration-[12s] ease-linear will-change-transform group-hover:translate-y-[var(--pan)]"
              />
            </div>
          </article>
        </Reveal>
      </div>
    </div>
  </section>

  <!-- Full-screen project detail view (reference style) -->
  <Teleport to="body">
    <div
      v-if="openProject !== null"
      class="fixed inset-0 z-50 overflow-y-auto bg-background/95 backdrop-blur-md"
    >
      <!-- Sticky glass top bar: brand (click = back) · close -->
      <div class="sticky top-0 z-20 border-b border-border/60 bg-background/80 backdrop-blur-xl">
        <div class="mx-auto flex w-full max-w-6xl items-center justify-between gap-4 px-5 py-2.5 sm:px-8">
          <button
            type="button"
            class="group flex cursor-pointer flex-col items-center leading-none"
            title="Back to home"
            @click="goHome"
          >
            <span
              class="font-display text-2xl font-extrabold leading-none tracking-tight text-foreground sm:text-[36px]"
            >
              <span class="brand-accent">H</span>amza<span
                class="brand-accent brand-accent-delay"
                >A</span
              >li<span class="text-copper">.</span>
            </span>
            <span
              class="mt-1.5 font-display text-[10px] font-bold tracking-tight text-foreground transition-colors group-hover:text-copper sm:text-[11px]"
            >
              Laravel Website Developer
            </span>
          </button>
          <button
            type="button"
            class="cursor-pointer rounded-full border border-border bg-card/50 p-2 text-muted-foreground transition-colors hover:border-copper/40 hover:text-copper"
            aria-label="Close project details"
            @click="closeDetail()"
          >
            <X :size="16" />
          </button>
        </div>
      </div>

      <div class="mx-auto w-full max-w-6xl px-5 pb-10 pt-4 sm:px-8">
        <!-- Draggable screenshot gallery: grab cursor, drag to move, wraps around -->
        <!-- Wide 5:2 frame (reference style) — full container width, fits the viewport -->
        <div class="mt-5 overflow-hidden rounded-2xl border border-border bg-ink-2">
          <div
            class="relative aspect-[5/2] w-full touch-pan-y select-none overflow-hidden"
            :class="
              galleryCanDrag
                ? galleryDragging
                  ? 'cursor-grabbing'
                  : 'cursor-grab'
                : ''
            "
            @pointerdown="galleryDown"
            @pointermove="galleryMove"
            @pointerup="galleryUp"
            @pointercancel="galleryUp"
          >
            <div
              class="flex h-full w-full"
              :style="{
                transform:
                  'translateX(calc(' +
                  -galleryIndex * 100 +
                  '% + ' +
                  galleryDragX +
                  'px))',
                transition:
                  galleryDragging || galleryNoAnim ? 'none' : 'transform 400ms ease',
              }"
            >
              <img
                v-for="(img, i) in gallerySlides"
                :key="i + '-' + img"
                :src="img"
                :alt="projects[openProject].title + ' screenshot'"
                class="h-full w-full shrink-0 select-none object-cover object-center"
                draggable="false"
              />
            </div>
          </div>
        </div>

        <!-- Project headline + tech chips (reference style, no Templates/GO LIVE) -->
        <div
          class="mt-6 rounded-2xl border border-border border-l-4 border-l-copper bg-card p-6 sm:p-8"
        >
          <h3
            class="font-display text-2xl font-bold tracking-tight text-foreground sm:text-3xl"
          >
            {{ projects[openProject].headline || projects[openProject].title }}
          </h3>
          <div class="mt-4 flex flex-wrap gap-2.5">
            <span
              v-for="tag in projects[openProject].tags"
              :key="tag"
              class="inline-flex items-center gap-2 rounded-full border border-border bg-secondary/40 px-3.5 py-1.5 text-xs font-medium text-foreground/90"
            >
              <component :is="tagIcon(tag)" :size="14" class="text-copper" />
              {{ tag }}
            </span>
          </div>
        </div>

        <!-- Overview + Highlights (items-start: each card hugs its own content) -->
        <div class="mt-6 grid items-start gap-6 lg:grid-cols-2">
          <!-- Overview -->
          <div
            class="rounded-2xl border border-border bg-card p-6 transition-all duration-300 hover:-translate-y-1 hover:border-copper/40 hover:shadow-[0_16px_40px_-18px_color-mix(in_oklab,var(--color-copper)_60%,transparent)] sm:p-8"
          >
            <div class="flex items-center gap-3">
              <span class="flex h-10 w-10 items-center justify-center rounded-xl bg-copper/15 text-copper">
                <Layers :size="20" />
              </span>
              <div>
                <p class="font-mono text-[10px] uppercase tracking-widest text-copper">
                  Overview
                </p>
                <h4 class="font-display text-xl font-medium text-foreground">
                  About This Project
                </h4>
              </div>
            </div>
            <p
              v-for="(para, i) in String(projects[openProject].overview).split('\n\n')"
              :key="i"
              class="mt-5 text-sm leading-relaxed text-muted-foreground"
            >
              {{ para }}
            </p>
          </div>

          <!-- Highlights -->
          <div
            class="rounded-2xl border border-border bg-card p-6 transition-all duration-300 hover:-translate-y-1 hover:border-copper/40 hover:shadow-[0_16px_40px_-18px_color-mix(in_oklab,var(--color-copper)_60%,transparent)] sm:p-8"
          >
            <div class="flex items-center gap-3">
              <span class="flex h-10 w-10 items-center justify-center rounded-xl bg-copper/15 text-copper">
                <CheckCheck :size="20" />
              </span>
              <div>
                <p class="font-mono text-[10px] uppercase tracking-widest text-copper">
                  Highlights
                </p>
                <h4 class="font-display text-xl font-medium text-foreground">
                  Key Features
                </h4>
              </div>
            </div>
            <ul class="mt-5 space-y-3">
              <li
                v-for="feature in projects[openProject].features"
                :key="feature"
                class="flex items-start gap-3 rounded-xl border border-border bg-secondary/40 px-4 py-3"
              >
                <Check :size="16" class="mt-0.5 shrink-0 text-copper" />
                <span class="text-sm leading-relaxed text-foreground/90">{{ feature }}</span>
              </li>
            </ul>
          </div>
        </div>

        <!-- Actions: Live demo + GitHub (hidden when a project has neither link) -->
        <div
          v-if="projects[openProject].demo || projects[openProject].github"
          class="mt-6 flex flex-col items-center gap-4 rounded-2xl border border-border bg-card p-6 sm:flex-row sm:justify-center sm:gap-6"
        >
          <a
            v-if="projects[openProject].demo"
            :href="projects[openProject].demo"
            target="_blank"
            rel="noopener"
            class="inline-flex w-full items-center justify-center gap-2 rounded-lg bg-primary px-6 py-3 text-sm font-medium uppercase tracking-wide text-primary-foreground transition-colors hover:bg-primary/90 sm:w-auto"
          >
            <ExternalLink :size="16" />
            View Live Demo
          </a>
          <a
            v-if="projects[openProject].github"
            :href="projects[openProject].github"
            target="_blank"
            rel="noopener"
            class="inline-flex w-full items-center justify-center gap-2 rounded-lg border border-border bg-card/50 px-6 py-3 text-sm font-medium uppercase tracking-wide text-muted-foreground transition-colors hover:border-copper/40 hover:text-copper sm:w-auto"
          >
            <Github :size="16" />
            Source on GitHub
          </a>
        </div>

        <!-- Back to projects -->
        <div class="my-10 flex justify-center">
          <button
            type="button"
            class="inline-flex items-center gap-2 rounded-lg border border-border bg-card/50 px-6 py-3 text-xs font-medium uppercase tracking-widest text-muted-foreground transition-colors hover:border-copper/40 hover:text-copper"
            @click="closeDetail()"
          >
            <ArrowLeft :size="14" class="text-copper" />
            Back to Projects
          </button>
        </div>
      </div>
    </div>
  </Teleport>
</template>
