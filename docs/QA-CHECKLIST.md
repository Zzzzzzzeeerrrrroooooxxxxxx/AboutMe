# Release QA checklist

## Before deploying
- [ ] Keep `backup/original-index.html` and a GitHub commit of the previous site.
- [ ] Create Supabase project and run `database/schema.sql`.
- [ ] Create one administrator in Supabase Auth and set `app_metadata` to `{"role":"admin"}`.
- [ ] Disable public sign-ups; enable MFA for the administrator.
- [ ] Add `PUBLIC_SUPABASE_URL` and `PUBLIC_SUPABASE_ANON_KEY` as GitHub Actions repository secrets.
- [ ] Set GitHub Pages source to GitHub Actions.

## Public website
- [ ] Home and all seven content pages load under `/AboutMe/`.
- [ ] Mobile menu opens/closes and all internal links keep `/AboutMe/` base path.
- [ ] Projects/research only show published records.
- [ ] Search filters projects and research.
- [ ] Long notes expand and remain plain text (no HTML execution).
- [ ] Activity shows public records only.
- [ ] Stack lists public technology entries.
- [ ] Contact form validates and inserts a message; honeypot blocks filled submissions.
- [ ] External links open with `noopener noreferrer`.

## Admin and database
- [ ] Correct admin account can sign in at `/AboutMe/admin/`.
- [ ] Non-admin account is denied admin access.
- [ ] Admin can create, edit, and delete project/research/activity/stack entries.
- [ ] Admin can mark contact messages read/unread.
- [ ] Anonymous user cannot read drafts or private activity.
- [ ] Anonymous user cannot write to content tables or read contact messages.
- [ ] No service-role or secret key appears in built JavaScript or repository.

## Deployment
- [ ] GitHub Actions build succeeds and Pages deploy job completes.
- [ ] Check `/AboutMe/` on desktop and mobile.
- [ ] Check `/AboutMe/admin/` and sign-in after deployment.
- [ ] Only replace/remove the old live site after these checks pass.

## Known production hardening
The contact form currently uses client validation, a honeypot, RLS and database constraints. Add Turnstile and server-side rate limiting before promoting the public form to high-traffic use.
