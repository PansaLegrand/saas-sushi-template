# Sushi SaaS 🍣

Launch‑ready SaaS starter with:

- Localized landing (next‑intl)
- Health endpoint (`/api/health`)
- Authentication server scaffold (Better Auth)
- Docs/blogs powered by MDX (Fumadocs)

This repo aims to be a clean base that you can ship fast and grow safely.

---

## Showcase

- DojoClip — https://dojoclip.com — Browser‑based video editing with laser‑focused multilingual subtitles.

---

## Contact

I have 5+ years of experience in NLP and I’m now doing a lot of full‑stack work. I speak English, French, and Chinese. I’m actively looking for remote opportunities and can help you build with this template — paid implementation or consultancy.

Contact: pansalegrand@gmail.com

---

## Contributor Guide

New contributors should review [Repository Guidelines](./AGENTS.md) for project structure, workflows, and review expectations before making changes.

---

## Quick Start

Prerequisites: Node 20+, pnpm 9+

```bash
pnpm install
pnpm dev
```

Open:

- Landing: `/en`, `/zh`, `/es`, `/fr`, `/ja`
- Health: `/api/health`
- Docs example: `/:locale/blogs/quick-start`
- Credit sandbox: `/:locale/credits-test`

---

## Project Structure

```
src/
├─ app/
│  ├─ [locale]/
│  │  ├─ page.tsx           # Landing page (i18n)
│  │  └─ (seo)/blogs/       # MDX docs/blogs (Fumadocs)
│  │     ├─ [[...slug]]/page.tsx
│  │     └─ layout.tsx
│  ├─ api/
│  │  ├─ health/route.ts    # Health endpoint
│  │  └─ auth/[...all]/route.ts # Better Auth handler
│  ├─ globals.css           # Tailwind + theme
│  └─ layout.tsx            # Root layout
├─ i18n/
│  ├─ locale.ts             # locales, defaultLocale, prefix
│  ├─ request.ts            # next-intl request config
│  ├─ routing.ts            # next-intl routing helper
│  └─ navigation.ts         # locale-aware Link/router
├─ lib/
│  ├─ auth.ts               # Better Auth server config
│  └─ utils.ts              # small helpers
├─ providers/
│  └─ theme.tsx             # theme + Toaster
└─ contexts/
   └─ app.tsx               # app-level provider (placeholder)

content/
└─ docs/<locale>/...        # MDX content (served at /:locale/blogs/...)

messages/
└─ <locale>.json            # landing + metadata translations
```

---

## Internationalization

This templated app uses next‑intl v4.

- Static routing config comes from `src/i18n/locale.ts`.
- Runtime message loading is in `src/i18n/request.ts`.
- Middleware is in `src/middleware.ts` (prefixed routes). We use `localePrefix: "always"` so `/en` and `/zh` etc. are explicit.
- Messages live in `messages/*.json`.

Add a locale:

1) Create `messages/<locale>.json`
2) Add the code to `locales` in `src/i18n/locale.ts`
3) Optionally add localized MDX pages under `content/docs/<locale>`
4) Restart dev

---

## Docs & MDX (Fumadocs)

- Content lives at `content/docs/<locale>/...`
- Routes are available at `/:locale/blogs/<slugs>`
- Dev script generates `.source/index.ts` automatically
- MDX is parsed by the Fumadocs Next plugin

Create a page:

```bash
mkdir -p content/docs/en
cat > content/docs/en/my-page.mdx <<'MDX'
---
title: My Page
---

# Hello
MDX
```

Then open `/en/blogs/my-page`.

Styling

- The blogs segment layout imports `fumadocs-ui/css/style.css` and wraps pages in `DocsLayout`.
- You can switch theme by importing a palette from `fumadocs-ui/css/*.css`.

---

## Authentication (Better Auth)

Better Auth powers both the server endpoints and the UI flows (sign-up, sign-in,
profile) in this template.

- Server config: `src/lib/auth.ts`
- Next API handler: `src/app/api/auth/[...all]/route.ts`
- Client helpers: `src/lib/auth-client.ts`
- UI routes: `/:locale/login`, `/:locale/signup`, `/:locale/me`
- Enabled endpoints: Email & Password (sessions stored in Postgres)

Environment variables:

```env
BETTER_AUTH_SECRET=<openssl rand -base64 32>
BETTER_AUTH_URL=http://localhost:3000
NEXT_PUBLIC_AUTH_BASE_URL=http://localhost:3000
```

### Database setup

Better Auth persists users, sessions, accounts, and verifications in Postgres via
Drizzle. Before testing auth, make sure:

1. `DATABASE_URL` is defined in `.env` (see `.env.example`).
2. Generate the latest SQL after schema changes:

   ```bash
   pnpm drizzle-kit generate --config src/db/config.ts
   ```

3. Apply migrations to your database:

   ```bash
   pnpm drizzle-kit migrate --config src/db/config.ts
   ```

4. Restart the dev server so Better Auth picks up the updated tables.

See `/en/blogs/database-setup` for the full guide.

---

## Health Check

`GET /api/health` returns JSON with service status, timestamp, and environment. Use this for readiness probes or basic uptime checks.

---

## Scripts

- `pnpm dev` – generates MDX map and starts Next with Turbopack
- `pnpm dev:webpack` – same but using Webpack (useful if Turbopack plugins misbehave)
- `pnpm build` / `pnpm start` – production build & start

---

## Email (Resend)

This template integrates transactional email via Resend.

- Code
  - Service: `src/services/email/send.ts` (sendMail, sendWelcomeEmail, sendPaymentSuccessEmail)
  - Templates: `src/services/email/templates/*` (welcome, payment-success)
  - Triggers: welcome on user create (`src/lib/auth.ts`), payment confirmation in Stripe webhook (`src/app/api/pay/webhook/stripe/route.ts`)
- Env

```env
RESEND_API_KEY=...
EMAIL_FROM="Your Name <founder@your-domain.com>"  # use your verified domain/subdomain
```

- Notes
  - Verify your domain in Resend; prefer a subdomain like `send.your-domain.com` and copy DNS values exactly.
  - Use a friendly From name and address (avoid `no-reply@`).
  - Emails are sent in the background; webhooks are acknowledged quickly.

Read the full guide at `/en/blogs/email-service` (also available in es/fr/ja/zh).

---

## Troubleshooting

- MDX “Unknown module type”: ensure the Fumadocs MDX plugin is active in `next.config.ts`, then restart (you should see `[MDX] types generated`).
- `/zh` shows English: verify `localePrefix = "always"` and restart; check `messages/zh.json` exists.
- Docs 404: confirm your file path under `content/docs/<locale>/...` matches the slug after `/blogs/`.


---

## Stripe

stripe login
stripe listen --forward-to localhost:3000/api/pay/callback/stripe
stripe trigger payment_intent.succeeded
---

## License

MIT. Contributions welcome.
