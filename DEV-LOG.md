# AAG Dev Log

A running log of anything that cost development time across AAG platforms (TSL, CIL, EO, and the shared stack). Shared by every Claude Code / Fable session and every developer.

**How to use this**
- When tooling fails mysteriously, **check this file first** — the cause is often one of the known gotchas below.
- When you lose time to a problem, **append an entry**: `date · project · symptom · fix`.
- Pull this repo at the start of a session; push after you append.

---

## Known recurring gotchas (CIL/TSL shared stack)

**Vercel env vars bake in at build time.**
Symptom: changed a `NEXT_PUBLIC_*` var but the site still uses the old value.
Fix: a plain redeploy reuses the cached build. Redeploy with **build cache OFF** (fresh build), or the change won't take.

**Supabase connection string — use the pooler, not the direct host.**
Symptom: app can't reach the DB; `EHOSTUNREACH` / IPv6 errors; works locally but not on Vercel.
Fix: use the **shared transaction pooler** string — `postgres.<ref>@aws-0-<region>.pooler.supabase.com:6543` — NOT the direct `db.<ref>.supabase.co` host (IPv6-only, unreachable from Vercel).

**`NEXT_PUBLIC_*` vars can't be marked "Sensitive" in Vercel.**
Symptom: Vercel blocks saving, or a var is stuck as secret and won't switch to config.
Fix: public-prefixed vars must be **plaintext/config** (they ship to the browser anyway). If one is stuck as secret, **delete and recreate** it as config.

**Env-var value must be pasted value-only.**
Symptom: var silently doesn't work.
Fix: paste `the-value`, never `KEY=the-value`. No `KEY=` prefix in the value box.

**Env-var scope crossing between prod and staging.**
Symptom: staging breaks after prod env setup; a var missing in one scope.
Fix: keep prod values in the **Production** scope, staging in **Preview** only. Same var name can hold different values per scope. Don't leave both on one shared scope.

**Watch for env-var name typos — they fail silently.**
Symptom: a feature (analytics, indexability) silently doesn't work; no error.
Fix: e.g. `EXT_PUBLIC_SITE_ENV` (missing the leading N) meant the site emitted `noindex` in production. Check the exact var name character-for-character.

**Supabase custom SMTP — the gotchas.**
Symptom: auth emails (reset, magic-link) don't send; nothing in Mandrill's log.
Fix: Mandrill SMTP port is **587 (STARTTLS)** — a wrong port (e.g. 566) kills the connection. Username can be anything (Mandrill accepts any); **password must be a valid Mandrill API key**, not the account password. Supabase magic-link/reset templates need the token in the link: `{{ .RedirectTo }}&token_hash={{ .TokenHash }}&type=magiclink` (or `type=recovery` for reset) — the default `{{ .ConfirmationURL }}` dead-ends if the app route reads `token_hash`.

**macOS Documents permission block (local dev on Jack's Mac).**
Symptom: the agent can't read files in `~/Documents` even though they exist.
Fix: either grant the terminal Full Disk Access (System Settings → Privacy & Security), or put files in the repo and commit them so the agent reads from the repo, not Documents.

**"Merged" ≠ "deployed" / "on the environment you're testing".**
Symptom: a fix is merged but you don't see it; or staging is behind main.
Fix: confirm the environment actually rebuilt. A domain re-aliases to a new branch deploy only when a fresh deploy on that branch runs. Sync main → staging and redeploy.

**`.jsonl` / data files may be gitignored.**
Symptom: files copied into the repo but the agent can't see them after push.
Fix: check `.gitignore`; force-add with `git add -f <file>` if ignored.

---

## Log entries

<!-- Append below: date · project · symptom · fix -->

2026-10-06 · TSL · `aag-shared` clone fails as `AllAboutGroup-Ltd/aag-shared` ("could not resolve to a Repository") · The repo actually lives at `jackdenton86/aag-shared` (personal account, public), not under the org. Clone from there.

2026-10-06 · TSL · Needed a prod credential (Google service-account JSON / CRON_SECRET) locally to test, but `vercel env pull` wrote the literal `[SENSITIVE]` instead of the value · Vars marked "Sensitive" in Vercel are write-only: never returned by a pull or shown in the dashboard. Get the same value from a non-Sensitive scope (local `.env.staging` / Preview), or exercise the credential via the deployed endpoint rather than locally.
