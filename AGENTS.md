# Project Notes: Slidev Deck (coding-agent-shogi-ai-talk)

Mermaid flowchart notes live in `docs/mermaid/index.md`.

## Editing Conventions

- `slides.md` is UTF-8. Read/write with UTF-8 to avoid mojibake.
- Mermaid blocks must use fenced code blocks: ` ```mermaid ` … ` ``` ` (not inline backticks).

## Slidev Layouts

- For “image on the right with a framed box”, use `layout: image-right-framed` (custom layout in `layouts/image-right-framed.vue`).
- Use `::right::` only for content that should appear under the framed image (the layout exposes a named slot).

## Mermaid + Images

- This deck enables Mermaid HTML labels via `setup/mermaid.ts`, so Mermaid text must be treated as trusted input.
- For node-embedded images, prefer a background-image `<div>` over `<img>` (Mermaid may inject restrictive styles into `<img>`).

## Line Breaking (Japanese)

- Avoid `overflow-wrap: anywhere` globally; it causes unnatural mid-word breaks.
- If a specific term must not break, use `<NW>...</NW>` (component in `components/NW.vue`) instead of repeated `<span class="nowrap">...</span>`.
