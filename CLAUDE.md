# Operational docs are in a separate (private) repo

This repository is the **public** companion that Yacht reads templates from over HTTPS — only `templates/*.template.yaml` and the like belong here. Anything describing the server's topology, IPs, recovery procedures, or other operational specifics is kept in a **private** repo to avoid making the homelab discoverable.

If you're an AI agent or developer with access to that private repo, open `~/dev/esprimo/CLAUDE.md` (or wherever your local clone of `gitlab.com:boterbram/esprimo.git` lives) for the actual context.

If you don't have access, ask the human; they will either share what's needed for the task or point you at the relevant section.

## What this repo *does* contain

- `templates/*.template.yaml` — Yacht v1 application templates, fetched by Yacht over HTTPS to deploy containers on the home server.
- `templates/caddy-jellyfin.Caddyfile.sample` — sample Caddyfile for the Jellyfin reverse-proxy setup.
- `README.md` — public-facing notes (server stats commands, HTTPS-for-Jellyfin walkthrough at the conceptual level).
- `scripts/`, `docs/` — currently unused. Operational docs live in the private repo, not here.

## Conventions when editing this repo

- Templates only. No topology, no IP addresses beyond the generic `192.168.2.100` already in the README, no recovery playbooks, no service inventories.
- Don't commit secrets — Yacht templates use `!secret_name` references that resolve from Yacht's secret store at deploy time; never replace those with actual values.
- Yacht reads templates directly from this repo via URL, so changes only reach Yacht after `git push` and a re-import / refresh in the Yacht UI.
