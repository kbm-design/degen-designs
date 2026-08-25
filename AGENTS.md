# AGENTS.md

## Cursor Cloud specific instructions

This is a single **Next.js 14** app ("DappArchive" — a Web3 dapp UI design archive) backed by
**PostgreSQL** via Prisma, with **Clerk** for auth and **Stripe** for payments. There is only one
service to run: the Next.js dev server. Standard scripts live in `package.json` (`dev`, `lint`,
`build`, `db:push`, `db:studio`, `seed`); this section only records the non-obvious bits.

### Startup (do this at the beginning of a session)

The update script only runs `npm install`. PostgreSQL is installed in the environment but is a
service that does not auto-start, so start it before running the app:

```bash
sudo pg_ctlcluster 16 main start
```

The app reads config from `.env` (git-ignored, so it persists in the VM snapshot but is not in git).
If `.env` is missing, recreate it with the values below. The Clerk/Stripe values are the committed
test keys already present in `.env.production`; the DB points at the local Postgres instance:

```
DATABASE_URL="postgresql://postgres:postgres@localhost:5432/dapp_archive"
NEXT_PUBLIC_APP_URL=http://localhost:3000
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_test_Zml0LWNvcmFsLTU0LmNsZXJrLmFjY291bnRzLmRldiQ
CLERK_SECRET_KEY=sk_test_ptW3JRuRqvuI8wT7nBU78YgOWBOn1e78KYVYlXACXH
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up
NEXT_PUBLIC_CLERK_AFTER_SIGN_IN_URL=/
NEXT_PUBLIC_CLERK_AFTER_SIGN_UP_URL=/
STRIPE_SECRET_KEY=sk_test_xxx
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_test_51RUctLER9pjsuH8QD2ozg4zmyEsWOYkbPMLXkoXyiHgF4nlT7ITKRloBNcI3PZTAIuUVYkVSwEGWiTLBtUUEkO9t00c9NaR3do
STRIPE_WEBHOOK_SECRET=whsec_xxx
```

### One-time DB bootstrap (only if the `dapp_archive` DB is empty/missing)

The DB and its seed data persist in the VM snapshot, so this is normally already done. If you need to
recreate it from scratch:

```bash
sudo -u postgres createdb dapp_archive   # if the database doesn't exist
npx prisma db push                       # create tables from prisma/schema.prisma
npm run seed                             # seeds GMX V2 + Jupiter dapps with sample images
```

### Running / verifying

- Dev server: `npm run dev` (http://localhost:3000). This is the intended way to run the app.
- Lint: `npm run lint` (passes clean).
- Health check: `curl -s http://localhost:3000/api/health` reports DB connectivity and row counts.

### Known caveats (pre-existing, not environment issues)

- `npm run build` (production build) currently **fails** at "Collecting page data" because
  `src/app/api/debug-public/route.ts` declares `export const runtime = 'edge'` but imports Prisma,
  which cannot run on the Edge runtime. This is a pre-existing code bug, unrelated to environment
  setup, and does not affect `npm run dev`.
- Seed images point to external Cloudinary URLs that are dead / CORS-blocked, so galleries render
  "Image unavailable" placeholders. The gallery layout, titles, and metadata still render correctly —
  this is expected with the sample data and is not an environment problem.
- Prisma Client is generated on `npm install` via the `postinstall` hook; if you change
  `prisma/schema.prisma`, re-run `npx prisma generate` (or `npx prisma db push`).
