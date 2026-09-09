# Production deployment

This fork deploys to the Cloudflare Worker `sink` through Workers Builds.
Changes pushed to `lainbo/Sink` on `master` automatically build and deploy production.

- Build command: `pnpm build`
- Deploy command: `pnpm deploy:worker`
- Root directory: `/`
- Node.js: `22`
- pnpm: `11.11.0`
- Non-production branch builds: disabled

## Cloudflare configuration

Set `DEPLOY_D1_DATABASE_ID` and `DEPLOY_KV_NAMESPACE_ID` in Workers Builds.
The deploy script generates the ignored `wrangler.deploy.jsonc` and applies D1
schema migrations before deploying. Keep production resource IDs in Cloudflare
settings rather than committing them to this repository.

Production reuses the existing D1 database `sink`, KV namespace `sink_kv`, and
Analytics Engine dataset `sink`. Keep their bindings named `DB`, `KV`, and
`ANALYTICS`. Workers AI uses the `AI` binding.

Configure `NUXT_SITE_TOKEN` and `NUXT_CF_API_TOKEN` as encrypted runtime secrets.
The analytics token is separate from the Workers Builds deployment token.
Keep runtime variables in Worker settings; `keep_vars` preserves them on deploy.
No R2 bucket is currently bound, and `NUXT_DISABLE_AUTO_BACKUP=true` disables the
scheduled R2 backup task.

## Production domains

The following native Worker custom domains serve the same application:

- `dux.cx`
- `lain.to`
- `ours.day`
- `u.lainbo.com`

Manage these domain bindings in Cloudflare. The Worker is also available at
`sink.lainbo.workers.dev`.

## Pages migration

Production moved from Pages to Workers on September 9, 2026. The existing link
storage and analytics dataset were retained. All 17 links were verified on all
four production domains, and the D1 backup passed a local restore check.

The old Pages project `sink` remains available at `sink-9tu.pages.dev` for
rollback. Its custom domain bindings and automatic builds are disabled.
