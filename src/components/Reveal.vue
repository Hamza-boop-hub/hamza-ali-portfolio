<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";

const props = defineProps({
  delay: {
    type: Number,
    default: 0,
  },
});

const el = ref(null);
const visible = ref(false);
let observer = null;

onMounted(() => {
  observer = new IntersectionObserver(
    ([entry]) => {
      if (entry && entry.isIntersecting) {
        visible.value = true;
        observer.unobserve(el.value);
      }
    },
    { threshold: 0.1, rootMargin: "0px 0px -50px 0px" }
  );
  observer.observe(el.value);
});

onBeforeUnmount(() => {
  if (observer) observer.disconnect();
});
</script>

<template>
  <div
    ref="el"
    class="transition-all duration-700"
    :style="{
      opacity: visible ? 1 : 0,
      transform: visible ? 'translateY(0)' : 'translateY(20px)',
      transitionDelay: `${props.delay}ms`,
      transitionTimingFunction: 'cubic-bezier(0.22, 1, 0.36, 1)',
    }"
  >
    <slot />
  </div>
</template>
