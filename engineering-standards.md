# AAG Engineering Standards

Consolidated architecture, guardrails and security standards for all AllAboutGroup platforms on the shared stack (TSL, CareerInLaw, Equal Opportunity Careers, and future builds).

**Purpose**
- One guide so every platform is built the same way.
- A template for new builds (starting with EO Careers and a future group site).
- A living document: it grows as each platform is built, and a security fix found on one platform is pushed across all of them (shared Vercel/Supabase stack and languages).

**Status:** STUB. The settled decisions are below. The full per-topic standards are still being amalgamated from the per-platform "lessons" documents (CIL and TSL exist; CDP, BI platform and marketing report tool still to be written and folded in).

_Maintained in the aag-shared repo. Last updated: 6 October 2026._

---

## 1. The standard stack
- **Framework:** Next.js (App Router)
- **CMS / content modelling:** Payload
- **Database + auth:** Supabase (Postgres), separate prod and staging projects per platform
- **Hosting:** Vercel
- **Edge / security / DNS front:** Cloudflare in front of Vercel
- **Consent management (CMP):** Cookiebot — the group standard, not an option
- **Transactional email:** Mandrill via Supabase custom SMTP
- **Newsletter:** Mailchimp (retiring Beehiiv across the group)
- **Bot protection:** reCAPTCHA v3

---

## 2. Architecture principles
- **Content pages static / CDN-served** (SSG/ISR); keep the database out of the read path to avoid crawler-load stampedes and for speed/SEO.
- **URL preservation** on any migration — legacy URLs kept 1:1 (the SEO asset); redirect maps with no chains.
- **Environment isolation** — prod and staging on separate Vercel scopes AND separate Supabase databases; never share a scope across environments.
- **Security by default** — RLS on all Supabase tables (invariant: 0 tables with RLS off); obscured CMS admin path + auth; edge rate-limiting.
- **Consent-gated analytics** — no analytics fire before the Cookiebot choice resolves.

---

## 3. Security & the fan-out principle
- The platforms share a stack, so a vulnerability or fix found on one is assessed and applied across all of them.
- RLS lockdown, obscured admin, consent-gating and the auth pattern (password-primary + email fallback) are shared standards, not per-platform choices.
- [To define] the security fan-out window / process — how fast a fix propagates and who signs it off.

---

## 4. Data protection & compliance
- UK GDPR posture consistent across platforms: controller is **AllAboutCareers Limited (06527117)**.
- Consent is per-named-partner, append-only, timestamped, copy-versioned; withdrawals honoured (latest-record-wins).
- Box 1 (marketing) and Box 2 (partner data-share) walled off from each other and from any special-category data.
- Special-category (diversity) data: internal/aggregate only, never shared, never used to score/rank/profile.
- Legal wording (privacy, terms, consent) adapted from the approved TSL set; material changes go to Gordons.

---

## 5. Build & deployment conventions
- Builds done in Claude Code.
- `NEXT_PUBLIC_*` vars bake in at build time — a plain redeploy reuses the cache; use a fresh build to pick up changes.
- Supabase connection from Vercel uses the **shared transaction pooler** (`pooler.supabase.com:6543`), never the direct host.
- See the shared **DEV-LOG.md** for the full list of recurring stack gotchas.

---

## 6. Open group-level decisions (to settle)
- Analytics standard: PostHog vs Mixpanel (CIL/TSL currently PostHog + GA4).
- Security fan-out window and process.
- DNS consolidation (TSL still on DreamHost; CIL on Cloudflare).
- Full retirement of Beehiiv in favour of Mailchimp + Mandrill across the group.

---

## 7. Target operating model (Sep 2026)
- Independent architecture review (Juan Fridano).
- Dev agency takes maintenance and security.
- Feature development moves to the people using the products (faster, more agile) — not all development, especially features.

---

## To amalgamate
- CareerInLaw lessons doc → fold in.
- TSL lessons doc → fold in.
- CDP, BI platform, marketing report tool → lessons docs still to be written, then folded in.
