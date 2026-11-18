# Luminac21 × StealthGrade — Full Website

A dark, monochrome engineering portfolio and research platform built with Astro (static output), Supabase Auth/Postgres/Storage, and GitHub Pages.

**Production URL:** https://zzzzzzzeeerrrrroooooxxxxxx.github.io/AboutMe/

## Included phases

1. **Foundation & design system** — responsive layout, navigation, metadata, mobile menu, reusable components.
2. **Public platform** — Home, About, Projects, Research, Stack, Activity, Collective, Contact.
3. **Content backend** — Supabase schema, public published-content reads, admin-only writes, message inbox, media bucket.
4. **Admin workspace** — email/password sign-in, `app_metadata.role=admin` gate, create/delete for projects, research, activity, technologies, and read-state controls for contact messages.
5. **Deployment** — GitHub Actions build/check/deploy to GitHub Pages, correct `/AboutMe` base path.
6. **Security & handover** — RLS policies, no service-role key in browser, setup and QA checklists.

## 1. Run locally

```bash
npm install
cp .env.example .env
npm run dev
```

Fill `.env` with the Supabase project URL and publishable/anon key. These two values are intended for browser use; never put a `service_role` or secret key in `.env` or frontend code.

## 2. Configure Supabase

1. Create a Supabase project.
2. Open **SQL Editor** and run `database/schema.sql`.
3. In **Authentication → Users**, create the one administrator user with email/password. Keep public signups disabled.
4. Open that user and set **App Metadata** to:

```json
{"role":"admin"}
```

Do not set this in user-editable metadata. `app_metadata` is controlled by an administrator and is used by the RLS policies.
5. Copy the Project URL and publishable (or legacy anon) key from **Project Settings → API**.
6. For local development, put them in `.env` as `PUBLIC_SUPABASE_URL` and `PUBLIC_SUPABASE_ANON_KEY`.

The admin route is `/AboutMe/admin/`. A signed-in user without the admin app-metadata claim is signed out and denied. Database RLS is the actual security boundary; hiding the admin UI is not treated as security.

## 3. Deploy to GitHub Pages

1. Replace the repository contents with this project (keep a backup of the previous `index.html` first).
2. In GitHub repository **Settings → Secrets and variables → Actions**, add:
   - `PUBLIC_SUPABASE_URL`
   - `PUBLIC_SUPABASE_ANON_KEY`
3. In **Settings → Pages**, set **Build and deployment → Source** to **GitHub Actions**.
4. Push the project to the `main` branch. The workflow runs `npm install`, `npm run check`, `npm run build`, and deploys `dist`.
5. Open the Actions tab and wait for the deploy workflow to finish. Verify `/AboutMe/` and `/AboutMe/admin/`.

## 4. Admin usage

- **Projects:** create draft/published/archived entries, category, summary, body notes, repository/demo URLs, featured flag.
- **Research:** create notes with slug, excerpt, body, category, reading-time estimate, and status.
- **Activity:** publish or keep private updates.
- **Stack:** add technologies and usage notes.
- **Messages:** view contact messages and toggle read/unread.

Content forms support create, edit, and delete for projects, research, activity, and stack entries. Research/project long-form notes can be expanded inline; content is escaped as plain text rather than interpreted as arbitrary HTML.

## 5. Security notes before public launch

- Keep only the Supabase publishable/anon key in the static frontend. Never expose `service_role`, database passwords, or other secret keys.
- Keep RLS enabled. Do not add broad anonymous write policies.
- The contact form has validation, a honeypot field, and DB constraints. For a high-traffic public launch, add Cloudflare Turnstile plus server-side rate limiting (Edge Function) before advertising the form widely; browser-only checks are not anti-spam security.
- The `site-media` bucket accepts common image formats up to 5 MB and only admin-authenticated uploads. Public reads are enabled for published media URLs.
- Set strong admin password, enable MFA in Supabase Auth, and do not share the admin account.
- Back up the database before schema changes.

## 6. Quality checks

```bash
npm run check
npm run build
npm run preview
```

Check: desktop/mobile nav, all internal links, no broken base paths, Supabase public reads, non-admin denied writes, admin create/delete, contact insert, and GitHub Actions deployment. This package has not been deployed to your GitHub account or connected to your Supabase project; those require your account credentials and explicit actions. A local dependency install/build could not be completed in this environment because the npm registry was unreachable (DNS/network timeout), so GitHub Actions is the first full build verification.

## Project structure

```text
src/
  components/       shared header/footer/cards
  data.ts           profile copy and social links
  layouts/          shared document layout
  lib/supabase.ts   public browser client
  pages/            public routes and admin workspace
  styles/           responsive design system
database/schema.sql Supabase tables, RLS, storage policies
.github/workflows/  GitHub Pages build/deploy
```
