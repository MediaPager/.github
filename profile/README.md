# MediaPager

<p align="center">
  <img src="https://github.com/MediaPager/MediaPager.App.Ui/blob/master/src/assets/images/icon.png?raw=true" alt="MediaPager play icon" width="160"><br>
  <strong>A self-hosted, plugin-driven media platform</strong>
</p>

MediaPager brings your media library together with extensible search, metadata, playback,
subtitle, and email providers. It combines an ASP.NET Core API, a Vue 3 + Quasar web app,
and a plugin SDK for official and community extensions.

## Quick start

Install Docker Engine and Docker Compose, then clone the deployment files:

```sh
git clone --depth 1 https://github.com/MediaPager/MediaPager.git
cd MediaPager
docker compose up -d
```

MediaPager runs from one Docker Hub image at **http://localhost:8080**. Compose creates the
network and persistent volumes automatically; no separate API/web containers or pre-existing
network are required. Use `MEDIAPAGER_MEDIA_PATH` to mount your media folder. On first run,
get the temporary admin password with `docker compose logs mediapager` and sign in as
`admin@mediapager.local` unless you set `MEDIAPAGER_SEED_USER`.

For local source development, see the
[superproject README](https://github.com/MediaPager/MediaPager#requirements-and-clone).

## Development run

```sh
git clone --recurse-submodules https://github.com/MediaPager/MediaPager.git
cd MediaPager
dotnet build MediaPager.slnx
```

Start the API and UI separately during development:

```sh
dotnet run --project MediaPager.App.Api/MediaPager.App.Api.csproj
```

```sh
cd MediaPager.App.Ui
npm ci
npm run dev
```

The API defaults to `http://localhost:5074`; the UI defaults to
`http://localhost:5173`. On first startup, a temporary password for
`admin@mediapager.local` is printed in the API log. Change it after signing in.

## Environment variables

This is a compact variable-name reference; the
[superproject README](https://github.com/MediaPager/MediaPager#environment-variables-and-configuration)
documents defaults, precedence, and deployment details. The API accepts `MEDIAPAGER_`-prefixed
configuration keys; use `__` between nested key segments. For example,
`MEDIAPAGER_Plugins__Directory` maps to `Plugins:Directory`.

- **UI:** `VITE_API_BASE_URL` (development default `http://localhost:5074`; production
  default `/`; runtime `runtime-config.json` can override it).
- **Database:** `MEDIAPAGER_DB_PATH`.
- **Authentication:** `MEDIAPAGER_SEED_USER`, `MEDIAPAGER_SEED_PASS`, `MEDIAPAGER_EKEY`.
- **Host/runtime settings:** `MEDIAPAGER_Frontend__BaseUrl`, `MEDIAPAGER_Email__Provider`,
  `MEDIAPAGER_Subtitles__Provider`, `MEDIAPAGER_Artwork__Directory`,
  `MEDIAPAGER_Plugins__Directory`, `MEDIAPAGER_Plugins__Required__Official__<index>`,
  `MEDIAPAGER_Plugins__Required__Community__<index>`, `MEDIAPAGER_AllowedHosts`,
  `MEDIAPAGER_Logging__LogLevel__Default`. The `MEDIAPAGER_` prefix can override any appsettings
  key; nested key segments use `__`.
- **Provider settings:** `MEDIAPAGER_TMDB_API_KEY`, `MEDIAPAGER_plugins__tmdb__imageBase`,
  `MEDIAPAGER_plugins__opensubtitles__apiKey`, `MEDIAPAGER_plugins__opensubtitles__username`,
  `MEDIAPAGER_plugins__opensubtitles__password`, `MEDIAPAGER_plugins__subdl__apiKey`,
  `MEDIAPAGER_plugins__smtp__host`, `MEDIAPAGER_plugins__smtp__port`,
  `MEDIAPAGER_plugins__smtp__username`, `MEDIAPAGER_plugins__smtp__password`,
  `MEDIAPAGER_plugins__smtp__from`, `MEDIAPAGER_plugins__smtp__enableSsl`,
  `MEDIAPAGER_plugins__mailgun__apiKey`, `MEDIAPAGER_plugins__mailgun__domain`,
  `MEDIAPAGER_plugins__mailgun__from`, `MEDIAPAGER_plugins__mailgun__apiBaseUrl`.
  Community plugin settings use the same dynamic
  `MEDIAPAGER_plugins__<pluginKey>__<setting>` pattern.
- **Private plugin installation:** `MEDIAPAGER_GIT_SSH_PRIVATE_KEY_PATH` points to a
  mounted deploy key; public HTTPS repositories need no key.
- **.NET/Docker runtime:** `ASPNETCORE_ENVIRONMENT`, `DOTNET_ENVIRONMENT`,
  `ASPNETCORE_URLS`, `PLAYWRIGHT_BROWSERS_PATH`. The image binds to
  `ASPNETCORE_URLS=http://0.0.0.0:5000` and sets `PLAYWRIGHT_BROWSERS_PATH=/ms-playwright`.
- **OS profile defaults:** `APPDATA` on Windows and `HOME`/the user profile on
  macOS/Linux affect the default database and plugin directories.
- **Compose `.env`:** `MEDIAPAGER_IMAGE` (Docker Hub image reference),
  `MEDIAPAGER_PORT` (defaults to `8080`),
  `MEDIAPAGER_MEDIA_PATH` (defaults to `./media`), `MEDIAPAGER_BASE_URL` (defaults to
  `http://localhost:8080`), `MEDIAPAGER_SEED_USER`, `MEDIAPAGER_SEED_PASS`,
  `MEDIAPAGER_EKEY`, and `MEDIAPAGER_TMDB_API_KEY`. Compose creates its own network and
  persistent data/plugin volumes. Optional external Docker tooling may use `DOCKER_CONFIG`
  or `WATCHTOWER_POLL_INTERVAL`; the supplied Compose file does not use them.

`MEDIAPAGER_EKEY` is the API's JWT signing key (minimum 32 bytes); it is not used to encrypt
the database. `MEDIAPAGER_SEED_USER` is an email/login used only when creating the first
super-admin; `MEDIAPAGER_SEED_PASS` sets that account's initial password. The TMDB API key
saved in plugin settings takes precedence over `MEDIAPAGER_TMDB_API_KEY`.

Settings saved in the application database take precedence over environment fallbacks.
Never put secret values in the public organization profile or source files.

## Projects

- **Application:** [API](https://github.com/MediaPager/MediaPager.App.Api) ·
  [Core](https://github.com/MediaPager/MediaPager.App.Core) ·
  [Plugin SDK](https://github.com/MediaPager/MediaPager.App.PluginContracts) ·
  [Web UI](https://github.com/MediaPager/MediaPager.App.Ui)
- **Official plugins:** [Local stream](https://github.com/MediaPager/MediaPager.Plugins.Stream.Local) ·
  [Local search](https://github.com/MediaPager/MediaPager.Plugins.Search.Local) ·
  [TMDB](https://github.com/MediaPager/MediaPager.Plugins.Search.Tmdb) ·
  [OpenSubtitles](https://github.com/MediaPager/MediaPager.Plugins.Subtitles.OpenSubtitles) ·
  [Subdl](https://github.com/MediaPager/MediaPager.Plugins.Subtitles.Subdl) ·
  [SMTP](https://github.com/MediaPager/MediaPager.Plugins.Email.Smtp) ·
  [Mailgun](https://github.com/MediaPager/MediaPager.Plugins.Email.MailGun)

## Community plugins

Community extensions use independent GitHub repositories named
`MediaPager.Plugins.<Type>.<Name>` and include a matching root manifest declaring their
capabilities and SDK compatibility. See the
[superproject README](https://github.com/MediaPager/MediaPager#plugin-development-and-discovery)
for development, configuration, and container deployment details.
