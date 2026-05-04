# preview-content

Static content for [preview.deltainstituut.nl](https://preview.deltainstituut.nl).
Netlify is gekoppeld aan deze repo: elke push naar `main` triggert een deploy.

## Structuur

```
.
├── _headers                          ← X-Robots-Tag noindex + cache rules
├── index.html                        ← homepage
├── fonts.css                         ← gedeelde fonts (Merriweather + Lora, base64)
├── og-image.jpg                      ← Open Graph image
└── artikelen/<slug>/index.html       ← artikelen
```

## Niet-indexeren

`_headers` zet `X-Robots-Tag: noindex, nofollow, noarchive, nosnippet` op alle paths.
Dit is server-level bescherming bovenop de inline `<meta name="robots">` in elk artikel.
Geen `robots.txt` met `Disallow: /` toevoegen — dat zou WhatsApp/Slack/LinkedIn link-previews breken.

## Een artikel toevoegen

1. Genereer de HTML via de [delta-html-documents skill](https://github.com/deltainstituut/delta-instituut-plugin) (of via een ander tool dat HTML in Delta-stijl produceert).
2. Plaats het bestand op `artikelen/<slug>/index.html`. Slug is kebab-case, geen spaties.
3. Zorg dat de HTML een `<meta property="og:url" content="https://preview.deltainstituut.nl/artikelen/<slug>/">` regel bevat.
4. Commit + push naar `main`. Netlify deployt automatisch.

```bash
git add artikelen/mijn-slug/
git commit -m "feat: add artikel mijn-slug"
git push
```

Status volgen: [Netlify dashboard](https://app.netlify.com/sites/preview-deltainstituut/deploys).

## Een artikel bewerken

Bewerk het bestaande `artikelen/<slug>/index.html`, commit, push.

## Een artikel verwijderen

```bash
git rm -r artikelen/<slug>/
git commit -m "remove: artikel <slug>"
git push
```

## Belangrijk

- Cache-control op `/fonts.css` is `immutable` (1 jaar). Als je `fonts.css` wijzigt en wilt dat browsers de nieuwe versie laden: hernoem naar `fonts.v2.css` (of een andere naam) en update de `<link>`-tags in alle artikelen, of forceer cache-bust via een query-string.
- `og-image.jpg` heeft 30 dagen cache. Vervangen kan, maar pas op met dimensies (1200×630).

## Build settings (Netlify dashboard)

- **Branch to deploy**: `main`
- **Build command**: *(geen — pure static site)*
- **Publish directory**: `.`
