# open-es.github.io

The OES website: <https://open-es.github.io>. The platform itself lives in
[open-enterprise-solutions/enterprise](https://github.com/open-enterprise-solutions/enterprise).

GitHub Pages builds it with Jekyll from `main` — nothing to run locally.

| Where | What |
|---|---|
| `index.html`, `ru/index.html`, `uk/index.html` | The home page, per language |
| `gallery.html`, `ru/gallery.html`, `uk/gallery.html` | The screenshots, per language; the home page shows the first of each group |
| `guide/`, `ru/guide/`, `uk/guide/` | The user's guide (the wiki's pages), per language |
| `_layouts/` | `default` — the page; `guide` — the contents and the previous/next links |
| `_data/i18n.yml` | The site's own words per language |
| `assets/css/site.css` | The look; `fonts.css` and `assets/fonts/` — IBM Plex, kept here (SIL OFL) |
| `assets/img/` | Screenshots; a `-light` / `-dark` pair follows the reader's system theme |
| `llms.txt`, `llms-full.txt` | What OES is, for a language model; the English guide in one file. Both are made from the guide's pages when the site is built |
| `<key>.txt`, `.github/workflows/indexnow.yml` | IndexNow: after each build of `main`, the sitemap's pages go to Bing, Yandex and the others that take it |

A page belongs to a language by its front matter `lang`, and to its translations by `ref`: the language
switch and the `hreflang` links find the page with the same `ref` in the other language.
