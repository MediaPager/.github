# MediaPager

<p align="center">
  <img src="https://github.com/MediaPager/MediaPager.App.Ui/blob/master/src/assets/images/icon.png?raw=true" alt="MediaPager play icon" width="160"><br>
  <strong>A self-hosted, plugin-driven media platform</strong>
</p>

MediaPager brings your media library together with extensible search, metadata, playback,
subtitle, and email providers. Its modular design keeps provider integrations in plugins,
so the application can grow without tying the host or interface to a single service.

## The platform

- **API:** ASP.NET Core on .NET 10, with authentication, persistence, and plugin hosting.
- **Web app:** Vue 3 + Quasar single-page application.
- **Plugin SDK:** Shared contracts for stream, search, metadata, subtitles, email, actions,
  and interface capabilities.
- **Extensible catalog:** Official plugins ship with MediaPager; community plugins can be
  discovered and installed through the application.
- **Self-hosted:** Run the API and web app locally or deploy the provided Docker images.

## Projects

| Project | Description |
|---|---|
| [MediaPager](https://github.com/MediaPager/MediaPager) | Superproject, .NET solution, deployment files, and development guide. |
| [MediaPager.App.Api](https://github.com/MediaPager/MediaPager.App.Api) | API host, accounts, data, and plugin lifecycle. |
| [MediaPager.App.Core](https://github.com/MediaPager/MediaPager.App.Core) | Domain services, plugin registry, and shared infrastructure. |
| [MediaPager.App.PluginContracts](https://github.com/MediaPager/MediaPager.App.PluginContracts) | SDK interfaces and data contracts for plugins. |
| [MediaPager.App.Ui](https://github.com/MediaPager/MediaPager.App.Ui) | Vue 3 + Quasar web client. |
| [MediaPager.Plugins.Stream.Local](https://github.com/MediaPager/MediaPager.Plugins.Stream.Local) | Local catalog playback. |
| [MediaPager.Plugins.Search.Local](https://github.com/MediaPager/MediaPager.Plugins.Search.Local) | Search across the local library. |
| [MediaPager.Plugins.Search.Tmdb](https://github.com/MediaPager/MediaPager.Plugins.Search.Tmdb) | TMDB metadata and search. |
| [MediaPager.Plugins.Subtitles.OpenSubtitles](https://github.com/MediaPager/MediaPager.Plugins.Subtitles.OpenSubtitles) | OpenSubtitles integration. |
| [MediaPager.Plugins.Subtitles.Subdl](https://github.com/MediaPager/MediaPager.Plugins.Subtitles.Subdl) | Subdl integration. |
| [MediaPager.Plugins.Email.Smtp](https://github.com/MediaPager/MediaPager.Plugins.Email.Smtp) | SMTP email delivery. |
| [MediaPager.Plugins.Email.MailGun](https://github.com/MediaPager/MediaPager.Plugins.Email.MailGun) | Mailgun email delivery. |

## Get started

Clone the superproject and all component repositories:

```sh
git clone --recurse-submodules https://github.com/MediaPager/MediaPager.git
cd MediaPager
dotnet build MediaPager.slnx
```

Run the API and web application in separate terminals:

```sh
dotnet run --project MediaPager.App.Api/MediaPager.App.Api.csproj
```

```sh
cd MediaPager.App.Ui
npm ci
npm run dev
```

The API defaults to `http://localhost:5074`; the development web app defaults to
`http://localhost:5173`. On first startup, a temporary password for
`admin@mediapager.local` is printed in the API log. Change it after signing in.

See the [superproject README](https://github.com/MediaPager/MediaPager#readme) for
requirements, configuration, Docker deployment, and plugin architecture.

## Community plugins

Community plugins are independent GitHub repositories named
`MediaPager.Plugins.<Type>.<Name>`. A matching repository-root manifest declares its
capabilities and SDK compatibility. MediaPager validates the manifest during discovery
and again during installation. Start with the
[plugin contracts](https://github.com/MediaPager/MediaPager.App.PluginContracts) and use
the [MediaPager organization](https://github.com/MediaPager) for official project
repositories.
