# Zion Agentic — mobile app package

## Content redesign (latest update)

Home, About Us, Services, Why Now, and Our Approach were all trimmed down to
their key messages — headline, one supporting paragraph, and the most
important 3-4 supporting points per section. Removed: the industry-by-industry
"Who We Serve" toggle system, the engagement-structure and stats cards on
Services, the trap-vs-opportunity comparison and cost-tax breakdown on Why
Now, and the full Cognitive Ascent camp-by-camp readiness/ROI tables on Our
Approach (kept as one short 3-layer summary instead). Every section still
ends in a "Book a Consultation" call to action.

**Left untouched, as requested:**
- The **Book a Consultation** tab — form, copy, and booking-slot picker are
  exactly as they were.
- The **"Unlock Our Approach"** gate (name/work-email form) on the Approach
  tab — still gates the content, just gates a shorter version of it.
- All navigation, theming, and the PWA/install functionality below.

I re-ran the same mobile screenshots and a scripted pass through every tab
checking for JS console errors after the content cut — nothing broke; the
only console messages are two pre-existing, harmless ones (Google Fonts
blocked in my sandbox, and an optional `content.json` the template always
tries and silently ignores if missing).

## What I changed (PWA pass)

Your original `index.html` was already largely mobile-responsive (it has a
real `@media(max-width:860px)` breakpoint that reflows the nav, grids, and
typography). I did **not** rebuild the design. I added the pieces that turn
a responsive *webpage* into an installable **app**:

1. **`manifest.webmanifest`** — the PWA manifest (name, icons, theme color,
   `display: standalone`). This is what lets a phone offer "Add to Home
   Screen" and open the site full-screen, without a browser address bar.
2. **`sw.js`** — a small service worker that caches the page and icons so
   the app still opens (with cached content) when the connection drops, and
   registers itself from `index.html`.
3. **`icons/`** — app icons generated from your existing brand mark (the
   indigo kite/compass logo already in your favicon), at the sizes iOS and
   Android expect (`192`, `512`, a `maskable-512` for Android's adaptive
   icon shapes, and a `180` Apple touch icon).
4. **Head tags in `index.html`** — links to the manifest/icons, plus the
   `apple-mobile-web-app-*` and `mobile-web-app-capable` meta tags iOS and
   Android look for.
5. **An "Install App" button** in the header — hidden by default, and shown
   automatically on browsers that fire the `beforeinstallprompt` event
   (Chrome/Edge/Android). Safari/iOS doesn't support that event — there,
   people install via Share → Add to Home Screen, same as any PWA.
6. **A small mobile fix**: form inputs now render at `16px` on screens
   under 860px. Below that size, Safari on iPhone auto-zooms the whole page
   when a field is focused — this stops that jump.
7. **`netlify.toml`** — cache headers so `sw.js` and the manifest are never
   served stale (which would otherwise leave visitors stuck on an old
   version of the app after you update it), while icons cache long-term.

I did not touch your booking form, the approach-unlock gate, the theme
toggle, or any of the copy/content logic — those work exactly as they did
in your uploaded file.

## Hosting it for free

Your forms already use Netlify's built-in form-handling attributes
(`data-netlify="true"`, hidden `form-name` fields) — that only works if the
site is actually deployed on Netlify, so that's the natural first choice.

### Option A — Netlify (recommended, since your forms are already wired for it)

1. Create a free Netlify account.
2. Drag the whole `zion-app` folder onto **Sites → Add new site → Deploy
   manually** in the Netlify dashboard (or connect a GitHub repo for
   auto-deploys — push this folder to a new repo first).
3. Once deployed, go to **Site settings → Forms → Form notifications** and
   add an email notification for each form (`consultation` and
   `approach-unlock`) pointing at your inbox. Until you do this, submissions
   still land safely in the Forms tab in the dashboard, just without an
   email alert.
4. Add your real domain under **Domain settings** if you have one — Netlify
   issues a free SSL certificate automatically.

**On Netlify's free tier:** I am not fully certain of the exact current
numbers — Netlify changed its free-tier model in the past year (moving to a
shared "credits" pool rather than a flat bandwidth/build-minute allowance),
and different sources I checked disagree on some details, likely because
some haven't been updated since the change. What I found consistently is
that form submissions are now free/unlimited on the free tier, and the free
tier overall is meant for low-traffic sites like this one. Please check
[netlify.com/pricing](https://www.netlify.com/pricing/) directly before you
rely on it, since this is exactly the kind of thing that can shift again.

### Option B — GitHub Pages or Cloudflare Pages (also free, no credit system)

Both host static files at no cost with no usage-based credits, which makes
them a safer long-term free option than Netlify if you'd rather not think
about tier limits at all. The trade-off: **your two forms will stop
working as-is**, because `data-netlify="true"` only means anything on
Netlify's infrastructure. To use either of these, you'd swap the form
`action`/submit logic to a third-party form backend instead — e.g.
[Formspree](https://formspree.io) or [Web3Forms](https://web3forms.com)
both have free tiers for a small volume of submissions. I don't have a
verified current submission cap for either — check their pricing pages
before committing.

- **GitHub Pages**: push this folder to a GitHub repo, enable Pages in the
  repo's Settings → Pages, and it deploys automatically on every push.
- **Cloudflare Pages**: connect the same repo (or drag-and-drop the folder)
  in the Cloudflare dashboard.

## Testing the app locally first

Service workers require HTTPS (or `localhost`) to register, so opening
`index.html` directly with `file://` won't show the install prompt. To test
before deploying:

```bash
cd zion-app
python3 -m http.server 8080
```

Then open `http://localhost:8080` in Chrome on your phone (or use Chrome
DevTools' device toolbar on desktop) — you should see the "Install App"
button appear, and DevTools → Application → Service Workers should show it
registered.

## File structure

```
zion-app/
├── index.html
├── manifest.webmanifest
├── sw.js
├── netlify.toml
└── icons/
    ├── icon-192.png
    ├── icon-512.png
    ├── icon-maskable-512.png
    └── apple-touch-icon.png
```
