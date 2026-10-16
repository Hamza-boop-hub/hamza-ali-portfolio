<template>
  <Transition name="scroll-top">
    <button
      v-show="visible"
      type="button"
      aria-label="Scroll to top"
      class="fixed bottom-6 right-3 z-[60] flex h-11 w-11 items-center justify-center rounded-full border border-copper/40 bg-gradient-to-b from-card/80 to-card/50 text-copper shadow-[0_10px_28px_-12px_color-mix(in_oklab,var(--color-copper)_70%,transparent)] backdrop-blur-md transition-all duration-300 hover:-translate-y-1 hover:border-copper hover:bg-copper hover:text-primary-foreground hover:shadow-[0_16px_34px_-10px_color-mix(in_oklab,var(--color-copper)_90%,transparent)]"
      @click="toTop"
    >
      <ArrowUp :size="18" />
    </button>
  </Transition>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";
import { ArrowUp } from "lucide-vue-next";

// Floating scroll-to-top control (reference style) — works on the page and
// inside the full-screen project detail overlay (its own scroll container).
const visible = ref(false);
let observer = null;
let poll = null;

const overlayEl = () =>
  document.querySelector("div.fixed.inset-0.z-50.overflow-y-auto");

const check = () => {
  const ov = overlayEl();
  const top = ov ? ov.scrollTop : window.scrollY;
  visible.value = top > 400;
};

// capture phase: catches window scroll AND the detail overlay's scroll
const onScroll = () => check();

const toTop = () => {
  const ov = overlayEl();
  const opts = { top: 0, behavior: "smooth" };
  if (ov) ov.scrollTo(opts);
  else window.scrollTo(opts);
};

onMounted(() => {
  window.addEventListener("scroll", onScroll, true);
  // Re-check when the project detail overlay mounts/unmounts (it teleports to
  // body) — opening a project must hide the button even if the page behind it
  // was scrolled; it reappears only after scrolling the overlay itself.
  observer = new MutationObserver(() => check());
  observer.observe(document.body, { childList: true, subtree: true });
  check();
  // lightweight fallback so the state never goes stale (covers environments
  // where scroll events are delayed or coalesced)
  poll = setInterval(check, 700);
});
onUnmounted(() => {
  window.removeEventListener("scroll", onScroll, true);
  if (observer) observer.disconnect();
  if (poll) clearInterval(poll);
});
</script>

<style scoped>
.scroll-top-enter-active,
.scroll-top-leave-active {
  transition:
    opacity 0.3s ease,
    transform 0.3s ease;
}
.scroll-top-enter-from,
.scroll-top-leave-to {
  opacity: 0;
  transform: translateY(14px);
}
</style>
