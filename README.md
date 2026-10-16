# Hamza Ali — Portfolio (Vue 3)

Personal portfolio site — single page (home + 404 route) with a dark copper/ink design,
scroll-reveal animations, project detail views with image sliders, and a preloader intro.

**Stack:** Vue 3 (`<script setup`) · Vite 7 · Vue Router · Tailwind CSS v4 · lucide-vue-next

## Run

```bash
npm install
npm run dev      # http://localhost:8082
npm run build    # production build -> dist/
npm run preview  # preview the built dist
```

## Structure

```
├── index.html              # meta/SEO tags, Google Fonts, preloader
├── public/
│   ├── hamza.jpg           # hero profile image
│   ├── favicon.ico         # branded "H" icon
│   ├── robots.txt
│   └── projects/           # project screenshots (real site captures)
└── src/
    ├── main.js
    ├── App.vue             # AmbientBackground + Navbar + router-view + Footer + ScrollTop
    ├── style.css           # Tailwind theme + animations
    ├── router/index.js     # home + 404
    ├── data/               # skills data
    └── components/         # Navbar, Hero, About, Skills, Experience,
                            # Projects, Services, TechStack, Contact,
                            # Footer, Reveal, ScrollTop, AmbientBackground
```

## Projects section

Each project card opens a full-screen detail view (overview, features, tag chips) with an
image gallery slider. Sweet Creamery and Sublime Logics use real site screenshots;
add new projects by extending the `projects` array in `src/components/Projects.vue`.
