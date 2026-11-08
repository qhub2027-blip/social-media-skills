---
name: "openverse-media"
description: "Find and attribute openly-licensed (CC / public domain) music, SFX and photos via the Openverse API for videos, sites and decks."
---

# Openverse Media

Search 800M+ openly-licensed images and audio for use in marketing videos, websites and decks — with correct attribution.

## API
- Base: `https://api.openverse.org/v1/`
- Endpoints: `GET /images/` and `GET /audio/` with `?q=<query>`.
- Useful params: `license` (e.g. `cc0,pdm` for no-attribution-needed; `by,by-sa` require credit), `license_type=commercial,modification`, `category` (audio: `music`, `sound_effect`; images: `photograph`, `illustration`), `source`, `page`, `page_size`, `extension`.
- Auth: anonymous works but is rate-limited. For higher limits, register a client at `/v1/auth_tokens/register/`, then OAuth2 client-credentials to get a token → `Authorization: Bearer <token>`.
- A result gives `url` (media file), `thumbnail`, `title`, `creator`, `license`, `license_version`, `license_url`, `foreign_landing_url`.

## Example
```bash
curl "https://api.openverse.org/v1/audio/?q=calm+corporate&category=music&license_type=commercial,modification&page_size=10"
```

## Workflow for a video/site
1. Query with `license_type=commercial,modification` so the asset is safe for client/commercial use and editable.
2. Prefer `license=cc0,pdm` when you want zero attribution burden; otherwise capture the attribution fields.
3. Download the `url`. (Note: in a restricted egress sandbox the media host may be blocked — fetch via an available browser and stage the file in.)
4. **Attribution:** for CC-BY/BY-SA build the credit line: "‹title› by ‹creator›, ‹license› (‹license_url›)." Keep a credits list for the project; put it in the video description / site footer.

## License cheat-sheet
- `cc0`, `pdm` → no attribution required (still nice to credit).
- `by` → credit required.
- `by-sa` → credit + share-alike — avoid for proprietary client work unless intended.
- `by-nc*` → non-commercial only — **do not** use for client/commercial videos.

## Note
For fully original, licence-free audio (bespoke music beds + SFX) the reel-video-studio skill synthesizes its own with numpy/scipy — use that when you want to avoid any attribution entirely.
