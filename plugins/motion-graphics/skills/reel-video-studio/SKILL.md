---
name: "reel-video-studio"
description: "Default for any video request (e.g. \"اعمل فيديو\"): vertical 1080x1920 marketing reel with GSAP motion, ElevenLabs voiceover and original synth music."
---

# Reel Video Studio

Render a polished vertical video by animating an HTML page with GSAP, seeking it frame-by-frame with puppeteer, and piping frames to ffmpeg. Audio is an ElevenLabs voiceover mixed under original synthesized music. No stock assets, no licensing.

When relevant, combine with the web-motion-graphics, web-3d-scenes and openverse-media skills.

## Workflow

1. **Script first.** Write a tight spoken script (~1 word/0.35s; 60s ≈ 160 words). Vary the videos: a features tour, a customer story (a named persona living the pain → the product fixing it), and an AI/differentiator angle. Keep one clear CTA + domain at the end.
2. **Voiceover (ElevenLabs connector).** `creative_list_voices` → pick a fitting voice (name it to the user). `creative_generate_speech` with `generations_count: 1` (avoid 429s and double-charges). Poll `creative_get_flow_run_status` until `all_completed`. For caption timing, `creative_transcribe_audio` with `connect_from` the TTS node gives per-word start/end seconds.
3. **Get the audio into the workspace.** The workspace cannot reach storage.googleapis.com. Open the signed mp3 URL in a browser; if a programmatic blob-download is blocked (Chrome's "multiple downloads" guard), open the URL in a *fresh tab* and use the native audio player's ⋮ → Download. Then stage the file from the user's Downloads folder into the workspace.
4. **Capture real screenshots** of the product/dashboard at a fixed size (e.g. 1395x868). Use the browser you have access to; respect any anti-bot protection (do not bypass it).
5. **Build the composition** (`page.html`) using the shared `common.css` + `common.js` helpers. Lay out scenes as absolute-positioned `.scene` blocks; animate on a single paused GSAP timeline. Expose `window.TOTAL`, `window.seek(t)`, and set `window.ready=true` after fonts+images load. Pan/zoom across screenshots with the `cam()`/`camTo()` helpers rather than showing them static. Add Arabic (RTL) captions synced to the word timings.
6. **QA before full render:** `node render.mjs page.html x 30 --stills t1,t2,...` then `python3 sheet.py out/sheet.jpg out/stills/*.jpg` and *look* at the contact sheet. Fix layout/RTL/overlap issues.
7. **Full render:** `node render.mjs page.html out/silent.mp4 30`.
8. **Music + mix:** write `cues.json` (dur, drop, outro, bpm, whoosh/pop/click/impact arrays at beat times), `python3 music.py cues.json music.wav sfx.wav`, then `DUR=<secs> ./mix.sh out/silent.mp4 vo.mp3 music.wav sfx.wav out/final.mp4`.
9. **Verify** the final has an aac audio stream and the right duration (`ffprobe`), then deliver the MP4(s).

## Key tooling (reusable)
- `render.mjs` — puppeteer launches `/tmp/chromium` (`@sparticuz/chromium`) with `--no-zygote --single-process --no-sandbox`; serves the page over a local http.server; `--stills` mode screenshots at given times; full mode seeks every frame and pipes JPEG (quality 93) to `ffmpeg -c:v libx264 -preset medium -crf 18 -pix_fmt yuv420p`.
- `common.js` helpers: `cam(rect,z)` focuses a rect of a screenshot in a fixed viewport; `hlBox`, `camTo(img,hl,rect,z,t,d)`, `frameIn`, `headIn`, `chipIn`, `sceneOut`, `show`, `hide`; `FIT` constant.
- `music.py` — numpy/scipy original bed: pad + bass + pluck arp + kick/hat in a minor key, sidechain-duck on the kick, master fades; plus whoosh/pop/click/impact SFX synths. Original, no licensing.
- `mix.sh` — VO loudnorm -16 then `apad=whole_dur=$DUR` (so the duck's sidechain doesn't cut the tail), music at ~0.55 with `sidechaincompress` ducked by the VO, SFX ~0.5, `amix`, final loudnorm -14, AAC 192k. **Always pass `DUR=<video length>`.**
- `sheet.py` — contact sheet for visual QA.

## Gotchas learned
- RTL + absolute screenshots: set explicit `left:0;top:0` on stacked `<img>` layers or they drift.
- Give typed-text overlays an opaque background so they don't bleed over placeholder text.
- Without `apad` on the VO, the mix goes silent after the last duck release.
- `generations_count: 1` on ElevenLabs; never re-call a generation to "retry" — it charges again.
- Theme each video distinctly (e.g. brand-navy for features, warm→calm for the story, dark-purple for AI) so a series doesn't look repetitive.
- Keep brand tokens central: colors, font (e.g. IBM Plex Sans Arabic), logo SVGs.
