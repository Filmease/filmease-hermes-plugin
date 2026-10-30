# FilmEase for Hermes Agent

Find a movie you can actually stream tonight, using your country, services, and movie taste. Save picks to your FilmEase watchlist, rate films, write private reviews, and record actual viewings and rewatches.

Published by Filmease LLC. Requires a FilmEase account and Hermes Agent **0.22.5 or later**. This portable Agent Plugins v1 package bundles a movie workflow skill and the production FilmEase Streamable HTTP MCP endpoint.

## Install and connect

Until the catalog submission is merged, install directly:

```bash
hermes plugins install Filmease/filmease-hermes-plugin --no-enable
hermes plugins enable filmease
```

Once listed, the install command is `hermes plugins install filmease`.

### Enable OAuth for the remote connection

Hermes's portable MCP format does not currently include an OAuth declaration. Add the following server block to your active profile's `config.yaml` (normally `~/.hermes/config.yaml`), merging it into any existing `mcp_servers` mapping:

```yaml
mcp_servers:
  filmease:
    url: https://filmease-mcp.fancy-fire-1eba.workers.dev/mcp
    auth: oauth
    trust: untrusted
```

The native server intentionally replaces the portable server of the same name, adding Hermes-managed OAuth while retaining approval prompts for writes. Keep `trust: untrusted` in this block. No API key, pasted access token, or FilmEase user ID is needed.

From a fresh terminal, complete the browser sign-in and FilmEase consent screen:

```bash
hermes mcp login filmease
```

Restart your Hermes conversation after connecting. The bundled workflow is available through Hermes's skill discovery as `filmease:filmease`. If a connection expires, run `hermes mcp login filmease` again. Complete initial login interactively before using remote or unattended sessions; this package does not automate consent or token handling.

## Try it

- “Find five uplifting movies under two hours I can stream tonight.”
- “Is Perfect Days streaming on my services?”
- “Show my FilmEase watchlist.”
- “Add After Yang to my watchlist.”
- “I watched Arrival last night and would give it a 9.”

Streaming availability depends on the film, country, and provider. The workflow uses returned availability facts and canonical movie IDs. Hermes asks for approval before write-capable MCP calls. FilmEase results remain usable as text; this package does not promise a Hermes movie-card UI.

## Network, permissions, and privacy disclosure

- The package contains declarative manifests and one Markdown skill. It runs no local executable, shell command, updater, background process, or filesystem reader.
- Hermes connects over HTTPS to `filmease-mcp.fancy-fire-1eba.workers.dev`. FilmEase handles sign-in and consent through its web application. Hermes owns OAuth credential storage and refresh; the plugin does not read credential files.
- Tool requests send the supplied movie intent, title, filters, or requested account changes to FilmEase. After authorization, personal tools can read or change the connected account's watchlist, ratings, private reviews, viewing history, companion labels, taste profile, country, and service preferences.
- The plugin adds no client telemetry or usage reporting. The remote FilmEase service records authenticated connection/tool activity, request summaries, latency, errors, and product outcomes as part of its existing service analytics. This service logging is not presented as opt-in plugin telemetry. Reviewers should assess this disclosure against the catalog's telemetry rule.
- FilmEase's hosted service uses Supabase and its movie/search/streaming infrastructure; using this plugin sends requests to that existing service. The package does not configure direct access to those backend providers.
- Reviews and companion labels are private unless the user explicitly requests otherwise. Authentication determines the account.

[Privacy policy](https://www.filmease.net/privacy) · [Terms](https://www.filmease.net/terms) · [Support](https://www.filmease.net/support) · info@filmease.net

## Maintenance

The Hermes catalog pins an exact reviewed commit. Package updates require a new catalog pull request with an updated SHA and version. There is no self-update mechanism.

This package reuses FilmEase's movie workflow from its Claude integration. Remote tool names and schemas are discovered from the service, rather than registered as native Python tools. It needs no environment variables or Python dependencies.
