# DappArchive Design System Audit

Visual inventory: open [`docs/design-system/index.html`](./index.html) in a browser.

Paper canvas: https://app.paper.design/file/01M09TDQHD1CKN5REW3HJMJQP3/3-1

## Snapshot

| Layer | Current state |
| --- | --- |
| Tokens | Default shadcn **slate** HSL vars in `src/app/globals.css` |
| Components | 16 shadcn/Radix primitives in `src/components/ui/` |
| Font | Inter via `next/font` in `layout.tsx` |
| Radius | `--radius: 0.5rem` |
| Dark mode | Tokens defined under `.dark`, not applied in root layout |
| Gallery | Bordered cards, dropdown filters, `aspect-[3/4]` screenshots |

## Intent for refresh

Neutral chrome so **apps and tabs carry color** — keep surfaces zinc/quiet; move accent into flow tabs / dapp identity; retire ad-hoc yellow/amber/blue/Clerk-green mismatches.

## Library note

Prefer **retoken + pattern polish on existing shadcn** first. Use [Astryx](https://astryx.atmeta.com/components) as layout reference (Segmented Control, Tab List, App Shell), not a full migration.
