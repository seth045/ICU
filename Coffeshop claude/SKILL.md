# Premium Website Builder

## Purpose

Build production-ready animated websites that feel worth $10,000+. The aesthetic target is [atelier-maren.netlify.app](https://atelier-maren.netlify.app/) — minimalist luxury, restrained motion, editorial typography, cinematic whitespace.

**Stack: Vanilla HTML · CSS · JavaScript · Python (when backend is needed)**

No framework overhead. Every deliverable runs by double-clicking or deploying a single folder.

---

## Input Modes

- **Scratch** — build from a brief or business description
- **Custom design** — translate uploaded mockups, screenshots, or PDFs into code
- **Scraped data** — rebuild an existing site as a premium experience using Firecrawl or web research

---

## Quality Reference

Study atelier-maren.netlify.app before every build. Internalize:

- Near-black or warm-neutral palette — never loud color
- Large serif or display heading, small clean sans body
- Sections breathe — generous padding, nothing crowded
- Imagery is the hero — let photos do the heavy lifting
- Interactions are whisper-quiet: subtle fades, slow image reveals, soft cursor behavior
- No carousels, no popups, no stock icons
- Mobile feels like a deliberate design decision, not a shrunk desktop

Every site built with this skill must clear that bar.

---

## Stack Reference

### Core (always)

```html
<!-- Smooth scroll -->
<script src="https://cdn.jsdelivr.net/npm/@studio-freight/lenis@1.0.42/dist/lenis.min.js"></script>

<!-- Animation engine -->
<script src="https://cdn.jsdelivr.net/npm/gsap@3.12.5/dist/gsap.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/gsap@3.12.5/dist/ScrollTrigger.min.js"></script>

<!-- 3D (only when the concept needs it) -->
<script src="https://cdn.jsdelivr.net/npm/three@0.165.0/build/three.min.js"></script>
```

### CSS foundation (every project)

```css
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

:root {
  --bg:      #0D0B09;
  --surface: #161310;
  --border:  rgba(255,255,255,0.07);
  --text:    #F0EBE3;
  --muted:   #7A6E65;
  --accent:  #C8A96E;
  --r:       clamp(1.5rem, 5vw, 5rem);   /* horizontal rhythm */
  --max:     1280px;
}

html { font-size: 16px; scroll-behavior: auto; } /* Lenis handles scroll */
body { background: var(--bg); color: var(--text); font-family: var(--font-body); -webkit-font-smoothing: antialiased; }
img, video { display: block; max-width: 100%; }
```

### Lenis setup (every project)

```js
const lenis = new Lenis({ lerp: 0.08, smoothWheel: true });
gsap.ticker.add((time) => lenis.raf(time * 1000));
gsap.ticker.lagSmoothing(0);
```

### Python backend (when needed)

Use **Flask** for contact forms, simple APIs, or CMS-light data serving.

```python
# app.py — minimal Flask backend
from flask import Flask, request, jsonify
from flask_cors import CORS

app = Flask(__name__, static_folder='dist', static_url_path='')
CORS(app)

@app.route('/')
def index():
    return app.send_static_file('index.html')

@app.route('/api/contact', methods=['POST'])
def contact():
    data = request.get_json()
    # send email, write to DB, etc.
    return jsonify({'ok': True})

if __name__ == '__main__':
    app.run(port=5000)
```

Install: `pip install flask flask-cors`  
Run: `python app.py`

---

## File Structure

### Single-file (standalone, no server needed)

```
project/
  index.html       ← everything: HTML + <style> + <script>
  assets/
    hero.mp4 / hero.jpg
    logo.svg
```

### Multi-file (preferred for larger sites)

```
project/
  index.html
  css/
    reset.css
    tokens.css     ← CSS custom properties
    layout.css
    components.css
    animations.css
  js/
    main.js        ← Lenis init, GSAP ScrollTrigger registration
    animations.js  ← all reveal / scrub / parallax logic
    three.js       ← 3D scene (only if used)
    cursor.js      ← custom cursor (only if used)
  assets/
    images/
    videos/
    models/        ← .glb files if 3D
  app.py           ← only if backend needed
  requirements.txt
```

---

## Intake: What to Extract Before Writing Code

When given any input (brief, design file, scraped site):

1. **Brand DNA** — 5 adjectives, primary palette (exact hex), fonts, mood
2. **Content inventory** — every section, headline, body copy, CTA
3. **Conversion goal** — what should visitors do?
4. **3D opportunity** — is there a scene that earns its weight? If not, skip 3D
5. **Animation map** — one entrance animation per section (see patterns below)

If input is scraped or researched: preserve all strong copy verbatim, rewrite generic copy, keep section order unless clearly broken.

---

## Page Structure (default)

1. **Navbar** — logo left, nav links right, hamburger on mobile, transparent → frosted on scroll
2. **Hero** — full viewport, dominant headline, one CTA, ambient motion or scroll video
3. **Stats / proof strip** — 3–4 numbers, no decoration needed
4. **About / story** — two-column: editorial text left, image right (or full bleed)
5. **Offer / services** — grid of 3–4 cards with hover reveal
6. **Showcase / gallery** — large images, staggered grid, cursor hover effect
7. **Testimonials** — minimal quote layout, no carousels
8. **Hours / location / contact** — practical info, clean table or list
9. **CTA** — one confident closing statement, one action
10. **Footer** — logo, links, copyright

Remove sections that don't fit the business. Add sections the business genuinely needs.

---

## Animation Patterns

### Scroll reveal (apply to every text block and card)

```js
gsap.utils.toArray('.reveal').forEach(el => {
  gsap.from(el, {
    scrollTrigger: { trigger: el, start: 'top 88%', once: true },
    opacity: 0, y: 40, duration: 0.9, ease: 'power3.out'
  });
});
```

### Staggered children

```js
gsap.utils.toArray('.stagger-parent').forEach(parent => {
  gsap.from(parent.children, {
    scrollTrigger: { trigger: parent, start: 'top 85%', once: true },
    opacity: 0, y: 28, duration: 0.7, stagger: 0.12, ease: 'power3.out'
  });
});
```

### Image parallax

```js
gsap.utils.toArray('.parallax-img').forEach(img => {
  gsap.to(img, {
    scrollTrigger: { trigger: img, scrub: 1.5 },
    yPercent: -18, ease: 'none'
  });
});
```

### Scroll-driven video scrub

```js
// Container must be 300vh+; video is position:sticky inside
const vid = document.querySelector('#scroll-video');
let target = 0, current = 0;
function lerp(a, b, t) { return a + (b - a) * t; }
function getProgress() {
  const wrap = vid.closest('.scroll-video-wrap');
  const r = wrap.getBoundingClientRect();
  const h = wrap.offsetHeight - window.innerHeight;
  return h > 0 ? Math.max(0, Math.min(1, -r.top / h)) : 0;
}
window.addEventListener('scroll', () => { target = getProgress(); }, { passive: true });
(function raf() {
  current = lerp(current, target, 0.08);
  if (vid.readyState >= 2 && vid.duration)
    vid.currentTime = current * vid.duration;
  requestAnimationFrame(raf);
})();
```

### Horizontal text marquee

```css
.marquee { overflow: hidden; white-space: nowrap; }
.marquee-inner { display: inline-block; animation: marquee 18s linear infinite; }
@keyframes marquee { from { transform: translateX(0); } to { transform: translateX(-50%); } }
```

### Custom cursor (luxury sites)

```js
const cursor = document.querySelector('.cursor');
const cursorDot = document.querySelector('.cursor-dot');
let mx = 0, my = 0, cx = 0, cy = 0;
window.addEventListener('mousemove', e => { mx = e.clientX; my = e.clientY; });
(function raf() {
  cx = lerp(cx, mx, 0.12); cy = lerp(cy, my, 0.12);
  cursor.style.transform = `translate(${cx}px,${cy}px)`;
  cursorDot.style.transform = `translate(${mx}px,${my}px)`;
  requestAnimationFrame(raf);
})();
// Expand on hoverable elements
document.querySelectorAll('a, button, [data-cursor]').forEach(el => {
  el.addEventListener('mouseenter', () => cursor.classList.add('expand'));
  el.addEventListener('mouseleave', () => cursor.classList.remove('expand'));
});
```

### Three.js ambient particle field (hero background)

```js
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(60, innerWidth/innerHeight, 0.1, 100);
const renderer = new THREE.WebGLRenderer({ canvas: document.querySelector('#bg-canvas'), alpha: true, antialias: true });
renderer.setSize(innerWidth, innerHeight);
renderer.setPixelRatio(Math.min(devicePixelRatio, 1.5));
camera.position.z = 5;

const count = 600;
const pos = new Float32Array(count * 3);
for (let i = 0; i < count * 3; i++) pos[i] = (Math.random() - 0.5) * 20;
const geo = new THREE.BufferGeometry();
geo.setAttribute('position', new THREE.BufferAttribute(pos, 3));
const mat = new THREE.PointsMaterial({ size: 0.04, color: 0xC8A96E, transparent: true, opacity: 0.5 });
scene.add(new THREE.Points(geo, mat));

(function animate() {
  requestAnimationFrame(animate);
  scene.rotation.y += 0.0003;
  renderer.render(scene, camera);
})();
window.addEventListener('resize', () => {
  camera.aspect = innerWidth/innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(innerWidth, innerHeight);
});
```

---

## Responsive Rules

```css
/* Fluid type scale */
--h1: clamp(2.8rem, 8vw, 7rem);
--h2: clamp(2rem, 5vw, 4rem);
--h3: clamp(1.25rem, 2.5vw, 1.75rem);
--body: clamp(0.95rem, 1.2vw, 1.1rem);

/* Section padding */
--section-y: clamp(5rem, 10vw, 9rem);

/* Mobile nav */
@media (max-width: 768px) {
  .nav-links { display: none; position: fixed; inset: 0; background: var(--bg); flex-direction: column; justify-content: center; align-items: center; }
  .nav-links.open { display: flex; }
}
```

Always test: desktop 1440px, laptop 1280px, tablet 768px, mobile 390px.

---

## Visual Design Rules

**Do:**
- One dominant font for headings (serif preferred: Playfair Display, Cormorant, DM Serif)
- One clean sans for body (Inter, DM Sans, Outfit)
- Palette: 2–3 colors max. Dark background or light background — never both on the same page
- Generous vertical rhythm — section padding minimum 80px, prefer 120px+
- Images fill their containers edge-to-edge at full bleed
- Borders and dividers at 6–8% white or black opacity
- Buttons: no shadows, no gradients — just border or flat fill with a confident color

**Don't:**
- Rainbow gradients or neon
- More than 2 typefaces
- Box shadows on everything
- Carousels or sliders
- Animated backgrounds that loop forever with no purpose
- Generic SaaS blue or startup purple

---

## Copy Rules

- Headline: outcome-first, maximum 8 words
- Subheadline: one specific, honest sentence about who this is for
- CTAs: verb + noun, no "Click here" (`Book a call`, `View the work`, `Get directions`)
- Body: 2–3 sentences max per block
- Keep all strong client copy verbatim; rewrite only what is vague or generic

---

## Delivery Formats

| Situation | Deliver |
|---|---|
| No server needed | Single `index.html` + `assets/` folder |
| Multiple pages or complex layout | Multi-file project folder |
| Contact form / data needed | Multi-file + `app.py` Flask backend |
| Scroll video | `index.html` + video file in same folder |

Always include a one-paragraph **how to run** note at the end of your response.

---

## Build Order

1. Extract brand tokens → CSS `:root` variables
2. Write semantic HTML skeleton (all sections, no styling)
3. Apply CSS: reset → tokens → layout → components
4. Add GSAP ScrollTrigger reveals to every section
5. Add Lenis smooth scroll
6. Add 3D scene if concept earns it (Three.js via CDN)
7. Add custom cursor if site is portfolio/luxury tier
8. Add scroll video scrub if video asset is provided
9. Wire Python backend if form or data endpoint is needed
10. Test at 390px, 768px, 1280px, 1440px

---

## Quality Bar

Before calling a site done:

- [ ] Hero communicates value in under 5 seconds
- [ ] Every section has at least one GSAP entrance animation
- [ ] Lenis smooth scroll feels buttery
- [ ] Mobile layout is intentional, not just functional
- [ ] Palette is consistent — no rogue colors
- [ ] Typography scale is visible — h1 is clearly h1
- [ ] All copy is specific and free of filler
- [ ] Images/video are optimized (no 10MB files)
- [ ] No console errors
- [ ] Reduced-motion: `@media (prefers-reduced-motion: reduce) { * { animation-duration: 0.01ms !important; } }`

The site must feel like it belongs on a design awards shortlist.
