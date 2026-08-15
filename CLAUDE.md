# Chess Trading Cards site

Single-page marketing site for a chess trading card set. One file,
`index.html`, ~1200 lines: inline CSS, inline JS, no build step, no
dependencies except three.js from a CDN and Google Fonts.

Deployed by GitHub Pages from `main` at
https://storiesnyc.github.io/chess-card-site/

---

## Writing rules (the owner cares about these a lot)

**Never use an em dash.** Not in copy, not in code comments, not anywhere.
Use a hyphen, a comma, a colon, or a new sentence. The owner's own printed
copy uses hyphens, so if you are moving a sentence across from a source
document, keep its punctuation exactly as printed.

**Do not write marketing copy.** Every title, subtitle and description on
this site must come from the owner's own documents (see Source documents
below). Do not invent a headline, a section intro, a product blurb or a
tagline, and do not paraphrase or shorten the owner's sentences to make
them fit a layout. Change the layout instead. If a slot needs words and no
source covers it, leave the slot empty and ask.

Interface microcopy is the one exception: button labels, filter names, the
pack gate prompt, alt text and aria-labels. Those may be written plainly
because no source document describes interface mechanics.

**No periods on section titles.** `<h2>` headings have no trailing period.
The hero `<h1>` keeps its period because it is the owner's own sentence.

**Nothing about the Kickstarter campaign.** It funded in May and is over.
No thresholds, no timelines, no "back this project", no fundraising framing.

**Only Monochrome card art appears on the site.** Cards #73 to #80 are the
only ones whose art is published. Do not add art for Commons, Uncommons,
Rares or Secret Rares to the page, and do not commit their image files:
anything in the repo is publicly downloadable from Pages. The two textures
inside the 3D pack (`cards/pack-card-common.png`, `cards/stack-back.png`)
are a deliberate exception the owner asked for.

---

## Source documents

Both are untracked in the repo root, deliberately. They are large and
should not be published.

**`First Edition Checklist/` (16 PNG page scans) is authoritative** for
anything about the set. Page 4 "The Opening Move!" holds the brand copy,
page 5 the rarity specs, pages 6/9/12/13 the per-tier intros, page 16 the
site link. Read the images directly.

**`kickstarter copy.pages`** holds backstory and process: the artist, the
immersion session, the goal. Use it for narrative, never for set facts.
Extract its text by decompressing the IWA streams:

```bash
python3 -c "
import zipfile,cramjam,re
z=zipfile.ZipFile('kickstarter copy.pages'); raw=z.read('Index/Document.iwa')
out=bytearray(); i=0
while i<len(raw)-4:
    ln=raw[i+1]|(raw[i+2]<<8)|(raw[i+3]<<16)
    try: out+=bytes(cramjam.snappy.decompress_raw(raw[i+4:i+4+ln]))
    except Exception: pass
    i+=4+ln
print(out.decode('utf-8','ignore'))" | strings
```

**Where they disagree, the checklist wins.** The Kickstarter describes a
64+8 set with no Monochromes; the real set is 80 cards.

Card art PDFs (`common (1).pdf`, `rare (1).pdf`, `one_of_one (1).pdf`,
`card_back.pdf`) carry live text, so card names, category labels and card
descriptions can be extracted with PyMuPDF rather than retyped.

---

## The set

80 numbered cards. Each card's rarity is shown by a chess piece symbol.

| Rarity | Numbers | Count | Symbol |
|---|---|---|---|
| Common | #1-32 | 32 | Pawn |
| Uncommon | #33-51 | 19 | Knight |
| Rare | #52-64 | 13 | Rook |
| Secret Rare | #65-72 | 8 | Queen |
| Monochrome | #73-80 | 8 | King |

Secret Rares are full art versions of eight cards already in the core set,
and Monochromes are versions of those same eight. That mapping is `CHAINS`
in the JS. Monochromes are one of each per print run.

**Known conflict:** the printed cards say **#15 = e4** and **#16 = d4**;
the owner's checklist has them the other way round. The site follows the
printed cards. Worth resolving before the next print run.

---

## index.html layout

- Inline `<style>` at the top. CSS custom properties in `:root` hold the
  palette, sampled from the pack artwork.
- `#gate`: full-screen 3D booster pack. Drag sideways to spin, drag down to
  tear open. `boot()` builds it; `EXIT_MS` is the whole tear sequence
  length. The pack front and back are real print artwork; eight card planes
  sit inside it.
- `#site`: the page. Sections are full-bleed `.band` wrappers with a
  `.wrap` inside. Do not put horizontal padding in a `padding` shorthand on
  `.band`, `.hero` or `.foot`: they share elements with `.wrap` and will
  silently zero its side padding.
- `var CARDS` (~line 510) is the 80-card dataset:
  `[number, name, categoryLabel, description, hasArt]`. The board, the
  checklist grid and the card detail all render from it.

---

## Working here

Push to `main` publishes to the live site within about a minute. There is
no staging. If a change should not be public yet, branch it.

This repo needs a raised post buffer or multi-file pushes fail with
HTTP 400 (`send-pack: unexpected disconnect`). Already configured locally:

```bash
git config http.postBuffer 524288000
```

To preview locally, serve over HTTP rather than opening the file. WebGL
refuses to load textures from `file://`:

```bash
python3 -m http.server 8765
```

The in-app browser pane does not repaint reliably at scroll offsets. To
screenshot a section, hide its siblings so it sits at the top of the
document instead of scrolling to it. Avoid CSS `zoom` to fit more in frame:
it shifts media query breakpoints and will show you the mobile layout while
you think you are looking at desktop.
