<script setup>
import { h, reactive, ref } from "vue";
import Reveal from "./Reveal.vue";
import { AlertCircle, CheckCircle2, Linkedin, Loader2, Mail, Send, X } from "lucide-vue-next";

// Official WhatsApp glyph (simple-icons) — currentColor so parent classes control the color.
const WhatsAppIcon = (_props, { attrs }) =>
  h(
    "svg",
    {
      viewBox: "0 0 24 24",
      width: attrs.size || 20,
      height: attrs.size || 20,
      fill: "currentColor",
      "aria-hidden": "true",
    },
    [
      h("path", {
        d: "M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.298-.497.099-.198.05-.371-.025-.52-.075-.149-.669-1.612-.916-2.207-.242-.579-.487-.5-.669-.51-.173-.008-.371-.01-.57-.01-.198 0-.52.074-.792.372-.272.297-1.04 1.016-1.04 2.479 0 1.462 1.065 2.875 1.213 3.074.149.198 2.096 3.2 5.077 4.487.709.306 1.262.489 1.694.625.712.227 1.36.195 1.871.118.571-.085 1.758-.719 2.006-1.413.248-.694.248-1.289.173-1.413-.074-.124-.272-.198-.57-.347m-5.421 7.403h-.004a9.87 9.87 0 01-5.031-1.378l-.361-.214-3.741.982.998-3.648-.235-.374a9.86 9.86 0 01-1.51-5.26c.001-5.45 4.436-9.884 9.888-9.884 2.64 0 5.122 1.03 6.988 2.898a9.825 9.825 0 012.893 6.994c-.003 5.45-4.437 9.884-9.885 9.884m8.413-18.297A11.815 11.815 0 0012.05 0C5.495 0 .16 5.335.157 11.892c0 2.096.547 4.142 1.588 5.945L.057 24l6.305-1.654a11.882 11.882 0 005.683 1.448h.005c6.554 0 11.89-5.335 11.893-11.893a11.821 11.821 0 00-3.48-8.413Z",
      }),
    ]
  );

const contactLinks = [
  {
    icon: Linkedin,
    label: "LinkedIn",
    value: "linkedin.com/in/hazma-ali-965101429",
    href: "https://www.linkedin.com/in/hazma-ali-965101429/",
  },
  {
    icon: Mail,
    label: "Email",
    value: "hamzamumtaz0600@gmail.com",
    // Gmail compose opens a ready-to-send draft in the browser (like wa.me does for WhatsApp)
    href: "https://mail.google.com/mail/?view=cm&fs=1&to=hamzamumtaz0600@gmail.com",
  },
  {
    icon: WhatsAppIcon,
    label: "WhatsApp",
    value: "+92 309 6717017",
    href: "https://wa.me/923096717017",
    whatsapp: true,
  },
];

const formState = reactive({
  name: "",
  email: "",
  subject: "",
  message: "",
});

const status = ref("idle"); // idle | sending | success | error
const replyTo = ref("");

const errors = reactive({
  name: "",
  email: "",
  subject: "",
  message: "",
});

const fieldRules = {
  name: (value) => (value.trim() ? "" : "Please enter your name."),
  email: (value) => {
    if (!value.trim()) return "Please enter your email address.";
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value.trim())
      ? ""
      : "Please enter a valid email address.";
  },
  subject: (value) => (value.trim() ? "" : "Please enter a subject."),
  message: (value) => (value.trim() ? "" : "Please enter your message."),
};

const clearError = (key) => {
  if (errors[key]) errors[key] = "";
};

const validate = () => {
  let firstInvalid = null;
  Object.keys(fieldRules).forEach((key) => {
    const message = fieldRules[key](formState[key]);
    errors[key] = message;
    if (message && !firstInvalid) firstInvalid = key;
  });
  return firstInvalid;
};

const dismissMessage = () => {
  status.value = "idle";
};

const handleSubmit = async () => {
  if (status.value === "sending") return;

  const firstInvalid = validate();
  if (firstInvalid) {
    status.value = "idle";
    const target = document.getElementById(`field-${firstInvalid}`);
    if (target) target.focus();
    return;
  }

  status.value = "sending";

  try {
    // Placeholder: replace this timeout with your real backend / email API call
    await new Promise((resolve) => setTimeout(resolve, 1200));

    replyTo.value = formState.email.trim();
    status.value = "success";
    formState.name = "";
    formState.email = "";
    formState.subject = "";
    formState.message = "";
    Object.keys(errors).forEach((key) => {
      errors[key] = "";
    });
  } catch (error) {
    status.value = "error";
  }
};
</script>

<template>
  <section id="contact" class="relative py-24 md:py-32">
    <div class="mx-auto max-w-7xl px-5 sm:px-8">
      <div class="grid grid-cols-1 gap-12 lg:grid-cols-12 lg:gap-16">
        <div class="lg:col-span-5">
          <div class="flex gap-4 sm:gap-5">
            <!-- About-style vertical badge rail on the left, content beside it -->
            <div class="flex shrink-0 flex-col items-center gap-4 pt-1">
              <Reveal>
                <span
                  class="font-display rotate-180 whitespace-nowrap text-sm font-medium uppercase tracking-[0.16em] text-copper [writing-mode:vertical-rl] sm:text-base"
                >
                  Contact
                </span>
              </Reveal>
              <span class="h-24 w-px bg-copper/40 sm:h-32"></span>
            </div>

            <div class="min-w-0 flex-1">
              <Reveal :delay="100">
                <h2 class="heading-lg text-3xl text-balance text-foreground sm:text-4xl md:text-5xl">
                  Let's Build Something Great Together
                </h2>
              </Reveal>
              <Reveal :delay="200">
                <p class="mt-6 text-balance leading-relaxed text-muted-foreground">
                  Have a project in mind? I'm open to freelance work, collaborations, and full-time
                  opportunities. Send me a message and I'll get back to you as soon as possible.
                </p>
              </Reveal>

              <div class="mt-8 space-y-3">
                <Reveal
                  v-for="(link, index) in contactLinks"
                  :key="link.label"
                  :delay="250 + index * 80"
                >
                  <a
                    :href="link.href"
                    :target="link.href.startsWith('http') ? '_blank' : null"
                    :rel="link.href.startsWith('http') ? 'noopener noreferrer' : null"
                    class="group flex items-center gap-4 rounded-xl border border-border bg-card/40 p-4 transition-all hover:border-copper/40 hover:bg-card"
                  >
                    <div
                      class="grid size-10 place-items-center rounded-lg bg-secondary text-copper transition-colors group-hover:bg-copper/20"
                    >
                      <component :is="link.icon" :size="20" />
                    </div>
                    <div class="min-w-0">
                      <p class="font-display text-sm font-medium text-foreground">
                        {{ link.label }}
                      </p>
                      <p class="truncate font-mono text-xs text-muted-foreground">
                        {{ link.value }}
                      </p>
                    </div>
                    <!-- WhatsApp badge (green, image-style) -->
                    <span
                      v-if="link.whatsapp"
                      class="ml-auto grid size-8 shrink-0 place-items-center rounded-full bg-[#25D366] shadow-[0_0_12px_rgba(37,211,102,0.35)]"
                      aria-hidden="true"
                    >
                      <WhatsAppIcon :size="15" class="text-white" />
                    </span>
                  </a>
                </Reveal>
              </div>
            </div>
          </div>
        </div>

        <div class="lg:col-span-7">
          <Reveal :delay="200">
            <form
              class="rounded-2xl border border-border bg-card/40 p-6 sm:p-8"
              novalidate
              @submit.prevent="handleSubmit"
            >
              <div class="grid gap-5 sm:grid-cols-2">
                <label class="block">
                  <span class="font-mono text-[11px] uppercase tracking-[0.15em] text-muted-foreground">
                    Name
                  </span>
                  <input
                    id="field-name"
                    v-model="formState.name"
                    type="text"
                    required
                    placeholder="Your name"
                    class="mt-2 w-full rounded-xl border border-border bg-background px-4 py-3 text-sm text-foreground outline-none transition-all placeholder:text-muted-foreground/50 focus:border-copper/60 focus:ring-1 focus:ring-copper/20"
                    @input="clearError('name')"
                  />
                  <p
                    v-if="errors.name"
                    class="field-error mt-1.5 text-[11px] font-medium tracking-wide text-rose-300/85"
                    role="alert"
                  >
                    {{ errors.name }}
                  </p>
                </label>
                <label class="block">
                  <span class="font-mono text-[11px] uppercase tracking-[0.15em] text-muted-foreground">
                    Email
                  </span>
                  <input
                    id="field-email"
                    v-model="formState.email"
                    type="email"
                    required
                    placeholder="you@example.com"
                    class="mt-2 w-full rounded-xl border border-border bg-background px-4 py-3 text-sm text-foreground outline-none transition-all placeholder:text-muted-foreground/50 focus:border-copper/60 focus:ring-1 focus:ring-copper/20"
                    @input="clearError('email')"
                  />
                  <p
                    v-if="errors.email"
                    class="field-error mt-1.5 text-[11px] font-medium tracking-wide text-rose-300/85"
                    role="alert"
                  >
                    {{ errors.email }}
                  </p>
                </label>
              </div>

              <label class="mt-5 block">
                <span class="font-mono text-[11px] uppercase tracking-[0.15em] text-muted-foreground">
                  Subject
                </span>
                <input
                  id="field-subject"
                  v-model="formState.subject"
                  type="text"
                  required
                  placeholder="Project inquiry"
                  class="mt-2 w-full rounded-xl border border-border bg-background px-4 py-3 text-sm text-foreground outline-none transition-all placeholder:text-muted-foreground/50 focus:border-copper/60 focus:ring-1 focus:ring-copper/20"
                  @input="clearError('subject')"
                />
                <p
                  v-if="errors.subject"
                  class="field-error mt-1.5 text-[11px] font-medium tracking-wide text-rose-300/85"
                  role="alert"
                >
                  {{ errors.subject }}
                </p>
              </label>

              <label class="mt-5 block">
                <span class="font-mono text-[11px] uppercase tracking-[0.15em] text-muted-foreground">
                  Message
                </span>
                <textarea
                  id="field-message"
                  v-model="formState.message"
                  rows="5"
                  required
                  placeholder="Tell me about your project, timeline, and goals..."
                  class="mt-2 w-full resize-none rounded-xl border border-border bg-background px-4 py-3 text-sm text-foreground outline-none transition-all placeholder:text-muted-foreground/50 focus:border-copper/60 focus:ring-1 focus:ring-copper/20"
                  @input="clearError('message')"
                />
                <p
                  v-if="errors.message"
                  class="field-error mt-1.5 text-[11px] font-medium tracking-wide text-rose-300/85"
                  role="alert"
                >
                  {{ errors.message }}
                </p>
              </label>

              <button
                type="submit"
                :disabled="status === 'sending'"
                class="mt-6 inline-flex items-center gap-2 rounded-xl bg-primary px-6 py-3 text-sm font-medium text-primary-foreground transition-all hover:bg-primary/90 hover:gap-3 disabled:cursor-not-allowed disabled:opacity-60"
              >
                <Send :size="16" />
                {{ status === "sending" ? "Sending..." : "Send Message" }}
              </button>

              <!-- Inline status message -->
              <div class="mt-5 min-h-[80px]">
                <transition
                  enter-active-class="transition-all duration-300 ease-out"
                  enter-from-class="-translate-y-1 opacity-0"
                  leave-active-class="transition-all duration-200 ease-in"
                  leave-to-class="-translate-y-1 opacity-0"
                >
                  <div
                    v-if="status === 'sending'"
                    class="flex items-start gap-3 rounded-xl border border-copper/30 bg-copper/10 px-4 py-3.5"
                    role="status"
                    aria-live="polite"
                  >
                    <Loader2 :size="18" class="mt-0.5 shrink-0 animate-spin text-copper" />
                    <div class="min-w-0">
                      <p class="text-sm font-medium text-foreground">Sending your message…</p>
                      <p class="mt-0.5 text-xs leading-relaxed text-muted-foreground">
                        Hang tight, this only takes a moment.
                      </p>
                    </div>
                  </div>

                  <div
                    v-else-if="status === 'success'"
                    class="flex items-start gap-3 rounded-xl border border-emerald-500/30 bg-emerald-500/10 px-4 py-3.5"
                    role="status"
                    aria-live="polite"
                  >
                    <CheckCircle2 :size="18" class="mt-0.5 shrink-0 text-emerald-400" />
                    <div class="min-w-0">
                      <p class="text-sm font-medium text-foreground">
                        Thank you! Your message has been received.
                      </p>
                      <p class="mt-0.5 text-xs leading-relaxed text-muted-foreground">
                        I'll reply to
                        <span class="text-foreground/80">{{ replyTo }}</span>
                        very soon.
                      </p>
                    </div>
                    <button
                      type="button"
                      class="-mr-1 ml-auto shrink-0 self-start rounded-md p-1 text-emerald-400/70 transition-colors hover:bg-emerald-500/15 hover:text-emerald-300"
                      aria-label="Dismiss message"
                      @click="dismissMessage"
                    >
                      <X :size="16" />
                    </button>
                  </div>

                  <div
                    v-else-if="status === 'error'"
                    class="flex items-start gap-3 rounded-xl border border-red-500/30 bg-red-500/10 px-4 py-3.5"
                    role="alert"
                  >
                    <AlertCircle :size="18" class="mt-0.5 shrink-0 text-red-400" />
                    <div class="min-w-0">
                      <p class="text-sm font-medium text-foreground">Something went wrong.</p>
                      <p class="mt-0.5 text-xs leading-relaxed text-muted-foreground">
                        Please try again, or email me directly — I'll reply as soon as possible.
                      </p>
                    </div>
                  </div>
                </transition>
              </div>
            </form>
          </Reveal>
        </div>
      </div>
    </div>
  </section>
</template>
