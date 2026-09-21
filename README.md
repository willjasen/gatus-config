# gatus-config

this repo tracks the services and endpoints i monitor with gatus.

the monitored targets include public websites, private tailscale hosted services, media and automation tools, and critical infrastructure checks for my home lab and self-hosted stack.

the public website list is sourced from my public github profile repository at https://github.com/willjasen/willjasen, which maintains the canonical list of personal sites, while the full config includes additional internal services and network checks.

the config file contains the current gatus endpoints for these monitored services.

as this is repo is updated, a github actions deploys a run that logs into my tailnet, remotes into my gatus instance, and updates its config with the new changes.

---

## Copilot Generated

## Automatic deployment

Changes merged to `main` are deployed automatically by
[`.github/workflows/deploy.yml`](.github/workflows/deploy.yml). The workflow
also supports a manual run from the **Actions** tab.

Create a `production` environment in the repository and add these secrets:

| Secret | Value |
| --- | --- |
| `GATUS_HOST` | Hostname or Tailscale hostname of the Gatus server |
| `GATUS_USER` | Linux user authorized by Tailscale SSH to update `/opt/gatus-config`, write the Gatus config, and restart `gatus` |
| `GATUS_DISCORD_WEBHOOK_URL` | Discord webhook used by the Gatus `discord` alerting integration and GitHub Actions run notifications |
| `TS_OAUTH_CLIENT_ID` | Tailscale OAuth client ID with permission to create a `tag:github-actions` device |
| `TS_OAUTH_SECRET` | Tailscale OAuth client secret |

Set `GATUS_HOST` to `gatus1` (or `gatus1.risk-mermaid.ts.net`). The Gatus
device must advertise `tag:gatus` and enable Tailscale SSH. On `gatus1`, run:

```sh
sudo tailscale up --ssh --advertise-tags=tag:gatus
```

If Tailscale is already configured on the host, use `sudo tailscale set
--ssh --advertise-tags=tag:gatus` instead. The host's enrollment/auth key
must be permitted to advertise `tag:gatus`; the workflow's ephemeral runner
uses `tag:github-actions`.

The deployment user needs Tailscale SSH authorization plus passwordless access
to the repository checkout, `/opt/gatus/config/config.yaml`, and the `gatus`
systemd unit. The workflow uses Tailscale SSH and `git pull --ff-only`, and
fails before restarting Gatus if any step fails. In Tailscale ACLs, allow
`tag:github-actions` to reach `tag:gatus` on TCP port 22 and authorize
`GATUS_USER` in the Tailscale SSH rules.
