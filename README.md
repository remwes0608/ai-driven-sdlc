# The AI SDLC Memo

Source for a GitHub Pages site about the AI-assisted SDLC: what gets written down, what a release
has to prove, and what it cost on real projects.

The theme is [Hydrogen](https://github.com/link9596/jekyll-theme-Hydrogen) (MIT, retained in
`licenses/`), **vendored into this repository** rather than pulled with `remote_theme`: it is six
templates and a few stylesheets, most of which are modified here, and a pinned copy cannot change
under the site. Pages builds it from the branch with no plugins outside the allowlist, so there is
no Actions workflow to maintain.

## Working on it locally

```sh
bundle install
bundle exec jekyll serve --livereload
```

The site is then at <http://127.0.0.1:4000/ai-driven-sdlc/> — note the `baseurl`, links break if
you drop it.

## Writing an article

Add a file to `_posts/` named `YYYY-MM-DD-slug.md`:

```markdown
---
title: "Anatomy of a buildable spec"
subtitle: "Six sections, and the one that most specs are missing"
description: "One sentence, ~160 chars. Used for meta description, Open Graph and JSON-LD."
---
```

`layout: post` is applied by the `defaults` block in `_config.yml`, so it does not need repeating.
A post gets its table of contents automatically; a **page** opts in with `toc: true` in front
matter, as `about.md` does. `description` is worth writing by hand — the fallback is the excerpt, which
is the first paragraph and rarely reads well out of context.

Cross-link other articles through the permalink so the `baseurl` is applied:

```liquid
[the previous article]({{ "/posts/the-spec-is-the-artefact/" | relative_url }})
```

## Dates

Post dates are spaced roughly three weeks apart, starting mid-July 2026. They are not the days the
text was typed. The material comes from twenty years of practice and was internal until it was
written up; consecutive daily dates would advertise volume, which is the opposite of the claim this
site makes. Keep the cadence when adding posts, and do not date anything in the future — Jekyll
silently drops future-dated posts and the only symptom is a page that looks unchanged.

## What was changed in the theme

The theme is vendored, so there is no override layer — `_layouts/`, `_includes/`, `_sass/` and
`assets/` are the site's own files now. What is worth knowing before editing them:

| Path | What it carries |
|---|---|
| `_includes/head.html` | Open Graph, Twitter card and JSON-LD metadata |
| `_includes/site-header.html` | The bar: title, links, and the social icons that stay visible on a phone |
| `_includes/toc.html` + `assets/js/toc.js` | Table of contents, built from each page's own `h2`s |
| `_includes/read_time.html` | Read time on a 5/15-minute ladder, not to the minute |
| `_includes/footer.html` | The AI-assistance note |
| `assets/css/extra.css` | The palette, the reading measure, and the blocks that would widen the page |

The palette is four CSS custom properties at the top of `extra.css`, each with its measured WCAG
contrast ratio in the comment beside it. Nothing on the site is under 5.5:1. Three of the theme's
own colours were replaced at source in `_sass/` because two of them failed AA outright; the Rouge
syntax colours are deliberately left alone.

Removed from the theme: the tab-title gimmick, the site-age counter, a third-party background image
API, MathJax, PJAX, and the menu script that reached for an element this markup does not have. Its
Chinese UI strings were translated — one of them broke the SCSS build, because libsass reads a
comment with no `@charset` as US-ASCII.

## Photographs

Two optional front-matter keys, both **large screens only**:

```yaml
background: /img/bands/forest-quiet.jpg   # photograph behind the title band
avatar: /img/portrait.jpg                 # portrait, floated right (About)
avatar_alt: "Remigiusz Weska"             # optional; defaults to site.author
```

Neither is hidden with `display: none` — each is declared inside a `min-width: 64em` media query
in the page's own `<style>` block, and a browser fetches a background image only when a rule using
it matches. A phone therefore downloads neither, and loses nothing: the band keeps its flat colour
and the portrait is decoration rather than content.

The band's scrim is not adjustable per page, and should not be. It is two stops of the site's own
green over the photograph, and it holds white text at 6.7:1 at the top of the band and 4.7:1 at
the bottom **against a pure-white photograph** — the worst case there is. It is already as light
as it can be while carrying that guarantee; lightening it further spends a future picture's
legibility on this one. It also pulls whatever
the picture is towards the band colour, so a page with a photograph and a page without read as the
same site.

**What each page's picture is about.** Forest, because the author likes being in one, and because
the two pages are two different ways of being in it. **About** is a journey: wandering, pleasure,
discovery — a path that bends out of sight, light coming through the canopy, no destination stated.
**Articles** is a road: one route, deliberately chosen, and you can see where it goes. The home
page can take either; a post takes one only if there is a reason for that post to have one.

That distinction is the whole brief. A reader will never articulate it, and will still feel that
the page about a person and the page listing work are not the same kind of place.

What a picture needs to be: landscape, at least 1600px wide, and quiet. Detail is lost under the
scrim, so texture at a distance — canopy, mist, bark, water — survives it and a busy subject does
not. Keep each file under ~250 KB; these are backgrounds, and nobody will zoom into them.

`img/card.png` is the 1200x630 social card referenced by `head.html`.

## One-time setup for publishing

1. Create the public repository and push this directory.
2. Settings → Pages → Source: **Deploy from a branch**.
3. Set `url` and `baseurl` in `_config.yml` to match where it lands. For a user site or a custom
   domain, `baseurl` is empty.
