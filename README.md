# Speero Research Recipe Hub — Embed

The interactive hub that runs on the `/research-recipes` and
`/research-methods` landing pages: problem browser, recipe directory, and the
filterable method directory. Data comes from the sync job, never from this
repo.

```
https://cdn.jsdelivr.net/gh/speerotools/research-hub-embed@v1.1.1/dist/embed.js
https://cdn.jsdelivr.net/gh/speerotools/research-hub-embed@v1.1.1/dist/embed.css
```

## Webflow install

One Code Embed block on the landing page:

```html
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,300;0,400;0,600;0,900;1,900&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/speerotools/research-hub-embed@v1.1.1/dist/embed.css">

<div id="speero-research-hub"></div>

<script>
  window.RESEARCH_HUB_CONFIG = {
    recipeBase: "/research-recipes/",
    methodBase: "/research-methods/"
  };
</script>
<script src="https://cdn.jsdelivr.net/gh/speerotools/research-hub-embed@v1.1.1/dist/embed.js"></script>
```

**Pin the version tag.** `@main` is mutable and jsDelivr caches it, so a bad
push reaches the site and a rollback does not. A tag is immutable, and
rolling back is a one-character edit in Webflow.

## Config

| Key | Default | What it does |
|---|---|---|
| `dataUrl` | the published CDN JSON | Where the data comes from |
| `mount` | `"app"` | Id of the element to render into |
| `recipeBase` | `"/research-recipes/"` | Recipe links go to the Webflow CMS page. `null` renders the in-embed detail view instead |
| `methodBase` | `"/research-methods/"` | Same for methods |
| `hubNav` | `true` | The hub's own nav: the only route to the problem browser, which has no URL of its own. Separate from the Webflow site nav above it |
| `suppressHero` | `false` | Landing view drops its hero, for a page that renders its own H1 and intro |
| `defaultRoute` | `""` | Which view to open with no hash in the URL. `"methods"` on the methods landing page, empty for the hub landing |

## Why detail links leave the embed

Every recipe and method has a real, indexed Webflow CMS page. If the embed
also rendered its own detail view at a hash URL, the same content would exist
at two addresses and the two would compete. So the embed owns browsing and
filtering, and the CMS owns the pages.

The landing page must also carry a plain-HTML list of every recipe and
method, outside this embed. That list is what passes authority into the 51
new URLs, and it has to exist with JavaScript turned off.

## Local testing

```bash
python3 -m http.server 8000
open "http://localhost:8000/test-hub.html?data=../research-hub-data/fixtures/research-hub.sample.json"
```

`test-hub.html` sets `recipeBase`/`methodBase` to `null`, so the in-embed
detail views render and the whole hub is browsable offline.

## Releasing

```bash
git tag v1.0.0 && git push --tags
```

Then bump the tag in the Webflow embed. Nothing else. The CSS is scoped
under `#speero-research-hub`, so it cannot reach the rest of the page.
