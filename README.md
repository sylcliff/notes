# notes

Personal notes site built with [Hugo](https://gohugo.io/) and the
[PaperMod](https://github.com/adityatelange/hugo-PaperMod) theme (as a git
submodule), deployed to [Cloudflare Pages](https://pages.cloudflare.com/).

## Local preview

```sh
git submodule update --init --recursive   # one-time, to fetch PaperMod
hugo server -D                            # drafts included
```

Open <http://localhost:1313/>.

## Cloudflare Pages deployment

| Setting            | Value                          |
| ------------------ | ------------------------------ |
| Project name       | `notes`                        |
| Subdomain          | `notes-6kd.pages.dev`          |
| Production branch  | `main`                         |
| Build command      | `hugo`                         |
| Build output dir   | `public`                       |
| Root dir           | `/`                            |
| Build image        | v3 (default)                   |
| Compatibility date | `2026-09-06`                   |
| Hugo version       | v0.147.7 (extended)            |
| Source             | GitHub → `sylcliff/notes`      |

These settings are mirrored in [`wrangler.toml`](./wrangler.toml) so a fresh
`wrangler pages deploy` from a clean checkout reproduces the same build.

### Why `notes-6kd.pages.dev` (not `notes.pages.dev`)?

`notes.pages.dev` was already taken on the global Pages subdomain pool, so
Cloudflare assigned `notes-6kd.pages.dev`. `hugo.toml`'s `baseURL` matches
this — change it (and the `wrangler.toml` `name` if you also rename the
project) if you bring your own custom domain.

### Deploying

Push to `main` and Cloudflare will build and deploy automatically:

```sh
git push origin main
```

A new preview URL is also created for every non-`main` branch push.

## Writing math

Inline: `$E = mc^2$`  
Block: `$$\int_{-\infty}^{\infty} e^{-x^2} dx = \sqrt{\pi}$$`

KaTeX is loaded via [`layouts/partials/extend_head.html`](./layouts/partials/extend_head.html)
and [`extend_footer.html`](./layouts/partials/extend_footer.html), which
override the empty PaperMod partials of the same name. The
`[markup.goldmark.extensions.passthrough]` block in `hugo.toml` keeps the
`$` / `$$` delimiters intact so KaTeX can find them.
