---
name: "web-motion-graphics"
description: "Animate web UI and motion graphics (GSAP, Lottie, scroll anatomy reveals, ai-kit) for videos, landing pages, hero sections and reels."
---

# Web Motion Graphics

A toolkit for professional 2D web animation. Pick the lightest tool that does the job; combine them for rich pages.

## GSAP (timelines, scroll, SVG)
- Load: `npm i gsap` or CDN `cdnjs.cloudflare.com/.../gsap/3.x/gsap.min.js` + `ScrollTrigger.min.js`. Register plugins: `gsap.registerPlugin(ScrollTrigger)`.
- Core: one `gsap.timeline()` per scene; `.to/.from/.fromTo`, position parameter for precise timing, `ease` (`power3.out`, `back.out(1.8)`, `expo.inOut`).
- Scroll: `ScrollTrigger` with `scrub:true` for scroll-linked, `pin:true` for sticky hero sections, `start/end` in `top center` syntax.
- For frame-exact video rendering, build the timeline `paused:true` and expose `window.seek(t)` (see the reel-video-studio skill).
- Respect `prefers-reduced-motion`: gate non-essential motion.

## Lottie / LottieFiles (vector animations)
- Use for designer-made JSON animations (icons, loaders, illustrations) exported from After Effects (Bodymovin) or lottiefiles.com.
- Web: `lottie-web` or the `<dotlottie-player>` / `<lottie-player>` web component. `npm i @lottiefiles/dotlottie-web`.
- Prefer `.lottie` (compressed) over raw `.json` for size. Control playback: `play/pause/setSpeed/goToAndStop`; drive progress from scroll or a GSAP timeline for scrubbed vector animation.
- Keep file size down: simplify paths, avoid huge comps; lazy-load below-the-fold players.

## Scroll "anatomy reveal" (sequential build-up)
- Pattern for revealing a diagram/product part-by-part as the user scrolls: pin a container, then a scrubbed timeline fades/scales each labeled part in sequence, optionally drawing connector lines (`strokeDashoffset`).
- Use `SplitText`-style line-by-line headline builds, staggered `opacity+y` for lists, and `clip-path`/mask wipes for images.
- Keep each reveal step tied to a clear scroll segment so it reads deliberately, not frantic.

## motiondivision ai-kit
- Generative/AI-assisted motion component kit — use for quick procedural/organic motion (noise-driven movement, generative backgrounds) when hand-authoring would be tedious.
- Install from its repo/npm, follow its component API; combine its canvas/background pieces with GSAP for foreground UI motion.

## Choosing
- Precise UI/scroll choreography → GSAP.
- Designer-delivered vector animation → Lottie.
- Explainer that builds a diagram on scroll → anatomy-reveal pattern (GSAP + ScrollTrigger).
- Generative/ambient motion → ai-kit.

## Gotchas
- RTL pages: set directional offsets by logical side, test Arabic layouts.
- Always `kill()`/`revert()` ScrollTriggers on route change in SPAs.
- Don't animate `top/left/width`; animate `transform`/`opacity` for GPU compositing.
