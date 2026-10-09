# MediaPager private operations and secrets guide

This private repository documents the configuration and credentials needed to operate
[MediaPager](https://github.com/MediaPager/MediaPager). It is an inventory and runbook,
not a place to commit live credentials. Keep secret **names, purpose, owner, and rotation
metadata** here; keep secret **values** in a dedicated password manager or deployment
secret store.

Private Git access is not a substitute for secret storage: credentials committed to Git
remain in repository history, clones, and backups after deletion. Never commit API keys,
passwords, tokens, signing keys, private keys, certificates, production `.env` files, or
database backups to this repository.

## Product configuration inventory

Record the location and rotation/expiry details for each value in your approved secret
manager. Do not put the value itself in this table.

| Secret or setting | Used for | Configure in MediaPager |
|---|---|---|
| `Auth:SigningKey` / `MPAGER_Auth__SigningKey` | Signing API bearer tokens; keep stable across restarts | .NET user-secrets for local development; inject from the deployment secret store in production |
| TMDB API key | Metadata and title search | Settings → Plugins → TMDB settings |
| OpenSubtitles credentials/API key | Subtitle search and downloads | Settings → Plugins → OpenSubtitles settings |
| Subdl API key | Subtitle search and downloads | Settings → Plugins → Subdl settings |
| SMTP username/password | Invitations and password-reset email | Settings → Email → SMTP |
| Mailgun API key | Invitations and password-reset email | Settings → Email → Mailgun |
| Optional Gmail/Microsoft OAuth credentials | Email delivery when a matching provider is installed | The installed email provider's settings |
| Git deploy key, if needed | Installing private plugin repositories | Mount as a file and set `MEDIAPAGER_GIT_SSH_PRIVATE_KEY_PATH` |
| Container-registry token, if used | Pulling private images from a registry | Docker/registry credential store; do not add it to the app `.env` |

The initial admin email (`MPAGER_SEED_EMAIL`) is configuration, not an authentication
secret. Use an address controlled by the operator. The generated first-run password is
printed once to the API log; retrieve it from the deployment logs, sign in, and change it
immediately.

## Deployment variables

The product repository's `.env.example` is the source of truth for the provided Compose
deployment. Copy it to `.env` in the **MediaPager checkout**, then set the deployment's
values there. The current Compose file requires:

| Variable | Purpose |
|---|---|
| `MPAGER_QNET_NETWORK` | Name of the pre-existing QNAP `qnet` network |
| `MPAGER_WEB_IP` | Unused LAN address assigned to the web container |
| `MPAGER_DATA_DIR` | Host directory for the database and persistent app state |
| `MPAGER_MOVIES_DIR` | Host directory mounted as the movie library |
| `MPAGER_SEED_EMAIL` | Email for the first-run super-admin account |

Other settings include the database path (`MPAGER_AUTH_DB_PATH`), plugin root
(`MPAGER_Plugins__Directory`), frontend base URL, and any provider-specific plugin
settings. Environment variables use the `MPAGER_` prefix; `__` represents nested
configuration sections. Check the product's `docker-compose.yml`, API `appsettings.json`,
and API README before adding deployment-specific overrides.

The app database and the `signing.key` stored beside it are sensitive. Restrict access to
the persistent data directory and its backups. Store backups encrypted and test restores.
Provider settings are managed in the application and must be included in operational
backup/recovery planning.

## Local development

Use .NET user-secrets for local API credentials rather than editing committed
`appsettings*.json` files. From the MediaPager checkout:

```sh
dotnet user-secrets set "Auth:SigningKey" "$(openssl rand -base64 48)" \
  --project MediaPager.App.Api/MediaPager.App.Api.csproj
```

Provider credentials are entered in the MediaPager UI under Settings after signing in.
Secret fields are redacted from settings responses. Do not copy real values into sample
configuration or support logs.

For containers, inject credentials from the host's secret manager or orchestrator. If a
deployment needs an environment-variable mapping that is not present in the supplied
Compose file, add it in the deployment's private configuration rather than committing a
value here.

## Access, rotation, and incident response

- Grant this repository only to operators who need access; review membership regularly.
- Prefer scoped, revocable provider keys and separate credentials per environment.
- Track a secret's owner, purpose, creation date, rotation date, and expiry in an approved
  password manager. Keep only non-sensitive inventory metadata here.
- Rotate credentials when an operator leaves, a key expires, access is uncertain, or a
  value may have been exposed. Update the provider and deployment, verify the integration,
  then revoke the old value.
- If a credential is committed or logged, revoke/rotate it immediately. Removing the file
  or rewriting Git history does not replace rotation; notify affected operators and review
  repository access and deployment logs.
- Never put production credentials in issue descriptions, pull requests, chat transcripts,
  screenshots, or build output.

## Related repositories

- [MediaPager superproject](https://github.com/MediaPager/MediaPager) — source, deployment
  examples, and component submodules.
- [MediaPager organization](https://github.com/MediaPager) — application, SDK, and official
  plugin repositories.
