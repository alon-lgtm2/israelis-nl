# CLAUDE.md — israelis.nl

## What This Is

A community hub for Israelis living in the Netherlands. The site aggregates 15+ community projects (events, sports leagues, professional networks, elections guides) into a single-page app with modal cards. All content is in Hebrew (RTL). The site is fully static — no backend, no database.

**Live URL:** israelis.nl

---

## Tech Stack

| Layer       | Technology                              |
|-------------|-----------------------------------------|
| Framework   | SvelteKit 2.5 + Svelte 4.2             |
| Build       | Vite 5.2                                |
| Adapter     | @sveltejs/adapter-static (prerendered)  |
| Styling     | Custom CSS (app.css) + scoped styles    |
| Fonts       | Heebo (body), Secular One (display)     |
| Analytics   | Umami (cloud.umami.is)                  |
| Auth        | Google OAuth 2.0 (Sign-In)              |
| Forms       | Web3Forms + Formspree.io               |
| Hosting     | Render (production + staging)           |
| CI/CD       | GitHub Actions → deploy on push to staging |

**No runtime dependencies** — `package.json` only has devDependencies (SvelteKit, Vite, Svelte).

---

## Project Structure

```
src/
├── app.html                    # HTML shell (Umami analytics injected here)
├── app.css                     # Global styles, CSS variables, animations
├── routes/
│   ├── +layout.js              # Prerender config (prerender = true)
│   ├── +layout.svelte          # Shared footer
│   ├── +page.svelte            # ★ MAIN FILE — homepage with all 12 cards
│   └── hayom/
│       └── +page.svelte        # "What are we doing today?" activities page
static/
├── elect/
│   └── index.html              # ★ Elections guide (standalone HTML + Tailwind)
├── favicon.svg
├── hayom-banner.jpeg
├── hitech-banner.jpeg
└── .nojekyll
Docs/
└── content.md                  # Hebrew project description (hayom background)
.github/workflows/deploy.yml   # CI/CD pipeline
render.yaml                    # Render.com static site config
```

### Key Files You'll Edit Most

- **`src/routes/+page.svelte`** — The main page. Contains all card data (hardcoded JS objects), modal logic, form handling, Google Sign-In integration. This is ~90% of the app.
- **`src/app.css`** — Design system: color variables, card themes, animations, responsive breakpoints.
- **`static/elect/index.html`** — Elections guide. Standalone page with its own styles (Tailwind via CDN). Contains city data, party platforms, eligibility checker, countdown timer.

---

## Content Architecture

All content lives as **hardcoded JavaScript objects** inside `+page.svelte`. There is no CMS, no JSON data files, no API calls for content.

### Card Object Shape

```js
{
  id: 'elect',           // URL-safe ID
  emoji: '🗳️',
  category: 'בחירות',     // Hebrew category label
  title: '...',
  desc: '...',
  status: 'live',        // 'live' | 'soon'
  theme: 'blue',         // Maps to CSS theme class
  heroStyle: '...',      // Inline CSS for hero gradient
  about: '...',          // Modal body text
  details: ['...'],      // Bullet points in modal
  links: [{ label, url }],
  hasForm: true,
  formLabel: '...',
  ctaLabel: '...',
  useGoogleSignIn: true
}
```

### Current Cards (12 total)

| ID          | Name               | Status | Google Sign-In | Notes |
|-------------|--------------------|--------|----------------|-------|
| elect       | בחירות (Elections)  | live   | No            | Links to /elect |
| hayom       | פעילויות (Activities)| live  | No            | Links to Facebook group |
| jobs        | קריירה (Careers)    | live   | No            | Links to jobs.israelis.nl |
| hitech      | טק (Tech)          | live   | No            | Links to Facebook group |
| nca         | ספורט (Basketball)  | soon   | Yes           | Web3Forms signup |
| papot       | הורות (Parenting)   | live   | Yes           | |
| saba        | משפחה (Family)      | live   | Yes           | Hosting elderly parents |
| holland     | מוזיקה (Music)      | live   | Yes           | Classical music WhatsApp |
| hagada      | חגים (Holidays)     | live   | No            | Links to sedermap.com |
| hatecheck   | AI                 | live   | No            | Links to hatecheck.nl |
| nifgashim   | שלישי (60+)        | live   | Yes           | |
| business    | עסקים (Business)    | live   | Yes           | |

---

## Design System

### CSS Variables (defined in `app.css`)

```css
--blue-deep: #1B2A4A;    /* Primary / dark backgrounds */
--orange: #FF6B35;        /* Accent / CTAs */
--green-nl: #21A366;      /* Secondary */
--cream: #FFF8F0;         /* Page background */
```

### Card Themes
Each card has a theme class (e.g., `.card-elect`, `.card-hayom`) that applies a unique gradient. Themes are defined in `app.css`.

### Responsive Breakpoints
- **700px** — Cards switch from grid to single column
- **640px** — Modal and hero adjustments

### Animations
- `fade-up` — Intersection observer triggered card entrance
- `float` — Floating shapes in hero section
- `scroll-bounce` — Scroll indicator pulse

---

## Third-Party Integrations

### Google OAuth 2.0
- **Client ID:** `225623142977-oqtmjp8lr7kt3n2b53ftl37801391021.apps.googleusercontent.com`
- Loaded dynamically via `loadGsi()` in `+page.svelte`
- Returns user profile (name, email, picture) on sign-in
- Used by 9 of 15 cards for form authentication

### Web3Forms
- **Access Key:** `4fbcbbf1-0fe6-4f59-986d-618f42b0df80`
- Receives form submissions with: name, email, profile picture, Google ID
- Currently used by NCA card

### Formspree
- Legacy form backend, form IDs stored per card
- Being phased out in favor of Web3Forms

### Umami Analytics
- **Website ID:** `fe4e09f5-6b61-4820-8704-716ef86776b6`
- Privacy-focused, no cookies
- Script in `src/app.html`, active on all routes

---

## Development

```bash
npm install          # Install dependencies
npm run dev          # Dev server at http://127.0.0.1:5173
npm run build        # Build to /build (static)
npm run preview      # Preview production build
```

### Branching & Deployment
- **`staging`** — Default working branch. Commit and push here by default.
- **`main`** — Production branch. Only push here when explicitly told to.
- **Staging URL:** https://israelis-nl-staging.onrender.com (Render, deploys from `staging`)
- **Production URL:** https://israelis.nl (Render, deploys from `main`)
- GitHub Actions also deploys to GitHub Pages on push to `staging`
- The elections page (`/elect`) is a standalone HTML file in `/static` — it deploys as-is, no build step

### RTL Considerations
- `<html lang="he" dir="rtl">` set in `app.html`
- All text alignment defaults to right
- Form inputs explicitly set `direction: rtl; text-align: right`
- Be careful with CSS `border-left` vs `border-right` — they're visually flipped in RTL

---

## Common Tasks

### Adding a New Card
1. Add a new object to the `cards` array in `src/routes/+page.svelte`
2. Add a theme class (`.card-{id}`) in `src/app.css` with gradient colors
3. If it needs a form: set `hasForm: true` and optionally `useGoogleSignIn: true`

### Adding a City to Elections Guide
1. Edit `static/elect/index.html`
2. Add a button to the city filter section
3. Add a city card `<div>` with council info, candidates, and party details
4. Update the JS filter logic if needed

### Modifying Styles
- Global changes → `src/app.css`
- Component-specific → `<style>` block in the relevant `.svelte` file
- Elections page → inline `<style>` in `static/elect/index.html`

---

## Architecture Notes

- **No component extraction** — The entire homepage is a single `+page.svelte` file. Cards, modals, forms, and auth are all in one file. This is intentional for simplicity given the project's scale.
- **No stores** — State is managed with local `let` variables in Svelte. Key state: `activeCard`, `formValues`, `formStatus`.
- **Elections page is isolated** — It's a standalone HTML file with its own Tailwind setup, not part of the SvelteKit routing. This means changes there don't affect the main app and vice versa.
- **All prerendered** — The SvelteKit adapter-static generates pure HTML. No server-side rendering at runtime.
