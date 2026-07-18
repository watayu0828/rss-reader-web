# rss-reader-web

Static pages hosted on **GitHub Pages** that act as the **Notion OAuth redirect landing**
for the Luno Reader app.

## Overview

This is a tiny static site (no build step, no dependencies). Its only job is to receive
the OAuth `code` that Notion returns after a user authorizes the integration, and hand
that code back to the mobile app.

It sits in the middle of the OAuth flow:

```text
Luno Reader app → Notion authorization → this page (redirect landing) → back to the app
```

The app registers this site's URL as the OAuth `redirect_uri`, so Notion sends the user
here with `?code=...` once authorization succeeds.

## Structure

| File | Purpose |
| --- | --- |
| `docs/index.html` | Top page. Currently an empty placeholder. |
| `docs/redirects/index.html` | Redirect handler. Reads the `code` and forwards it back to the app. |
| `docs/redirects/fallback.html` | Shown when authorization fails (no `code` present). |

## How the redirect works

`docs/redirects/index.html` runs a small client-side script that inspects the `code`
query parameter:

```text
?code missing        → redirect to fallback.html (authorization failed)
?code present, Android → redirect to  luno-reader://notion-auth?code=<code>
?code present, other   → redirect to same URL with ?code=<code>
```

- **Android** uses the `luno-reader://notion-auth` custom scheme to return control to the
  app (opened via an in-app browser / Custom Tab).
- **iOS and other** platforms re-navigate to the same URL carrying `?code=...`, which the
  app's in-app WebView captures.

## Deployment

The site is served by **GitHub Pages** from the `docs/` folder on the `main` branch.
Pushing to `main` publishes the changes.

- Base URL: `https://watayu0828.github.io/rss-reader-web/`
- Redirect landing: `https://watayu0828.github.io/rss-reader-web/redirects/`
