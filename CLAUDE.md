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

**No periods on any title.** Headings carry no trailing period. This now
includes the hero `<h1>` and the footer line, which an earlier version of
this file wrongly exempted as "the owner's own sentence". The owner
corrected that on 2026-08-15: no title anywhere ends in a period.

**Do not invent badges, eyebrows or spec strips.** The hero had a
"First Edition &middot; 80 cards &middot; Printing 001" pill above the
headline. The owner cut it: that kind of manufactured metadata chip reads
as generated filler. Numbers belong where the owner put them, such as the
tally under the hero, not decorating a headline.

**The Kickstarter is backstory, not a campaign.** It funded in spring 2026
and is over. On 2026-08-15 the owner asked for a short "How we got started"
note at the end of the Art section: ran a Kickstarter in spring 2026, raised
almost $7,000, which secured enough interest to illustrate and print a
complete run of the first set. That is the whole of it. Still no thresholds,
no timelines, no "back this project", no fundraising framing, and no live
campaign language anywhere else on the page.

**Do not name other card brands.** The Art section once read "handling
Pokémon and Yu-Gi-Oh! cards"; the owner changed it to "handling our
favorite trading cards". Keep other people's trademarks off the page.

**Only Monochrome card art appears on the page itself.** Cards #73 to #80
are the only ones whose art is published in the page body. Do not add art
for Commons, Uncommons, Rares or Secret Rares to the page, and do not commit
their image files: anything in the repo is publicly downloadable from Pages.

The 3D pack interior is a deliberate exception the owner asked for. It holds
eight `cards/pack-card-*.png` textures, published knowingly:

| Card | Rarity | File |
|---|---|---|
| #1 Pawn (Black) | Common | `pack-card-common.png` |
| #4 Knight (White) | Common | `pack-card-004.png` |
| #11 Queen (White) | Common | `pack-card-011.png` |
| #21 Sicilian Defense | Common | `pack-card-021.png` |
| #32 Blitz Chess | Common | `pack-card-032.png` |
| #43 Zugzwang | Uncommon | `pack-card-043.png` |
| #47 Fork | Uncommon | `pack-card-047.png` |
| #78 The Gold Coin Game | Monochrome | `pack-card-078.png` |

Adding any further tier to that list is the owner's call, not a judgement
call. `cards/stack-back.png` is the old card back, no longer referenced.

These are a separate set from the `cards/card-0NN.png` the page body uses.
The page builds those paths in `imgSrc()`, so do not repoint it at a
`pack-card-` file: the two sets are cut differently. Regenerate the pack set
with the script in this file's history, which crops the print PDF's bleed
down to the drawn card and resizes to 384x536, the real 63x88 mm trim ratio.
Keep them RGB palette PNGs. Alpha corners were tried and cost 3x the bytes
on this halftone-heavy art, so the rounding is done in geometry instead.

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

Card art PDFs (`common (1).pdf`, `uncommon (1).pdf`, `rare (1).pdf`,
`one_of_one (1).pdf`, `card_back.pdf`) carry live text, so card names,
category labels and card descriptions can be extracted with PyMuPDF rather
than retyped. Page order is card order: page N of `common (1).pdf` is card
#N, and page N of `uncommon (1).pdf` is card #(32+N). Titles are not the
first text block on a page, so sort blocks by y before reading them.

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
  palette. **The design follows the First Edition Checklist**, not the pack
  poster styling the site started with: off white stock, hairline rules,
  soft shadows, rounded corners, sentence case headings, and Inter as the
  single family for headings and body. Two motifs carry the print pieces
  across: the red to amber gradient rule that closes every checklist content
  page (`.rulebar`, `--band-grad`), and the halftone dot field from the pack
  and box fronts.
- Rarity has one colour set, sampled from the glyph strip on checklist page
  5, used for the ladder glyphs, board squares, tier bars and chain steps:
  Pawn `--pawn` cyan, Knight `--knight` periwinkle, Rook `--rook` light
  blue, Queen `--queen` blue, King `--king` purple. `--pawn-lt/-dk` and
  `--knight-lt/-dk` are the dropped back tints the board needs so card
  numbers stay readable. `--cream`, `--yellow`, `--blue` and `--magenta`
  survive only as aliases so older rules keep resolving.
- `#gate`: full-screen 3D booster pack. Drag sideways to spin, drag down to
  tear open. `boot()` builds it; `EXIT_MS` is the whole tear sequence
  length. The pack front and back are real print artwork; eight card planes
  sit inside it.
  - Everything is derived from real millimetres. The bag is 70x130mm at
    `W=1.0`, so a world unit is 70mm and the cards must be `W*63/70` wide.
    `BULGE` is half the pack's thickness, about 4mm for eight cards.
  - The camera is locked for the whole tear. Do not add a dolly: a card is a
    fixed object and must not change on-screen size while it is visible.
  - The tear strip is the left edge, so cards may only leave that way. The
    stack never slides relative to the pack; framing moves the whole `group`
    so a card can never appear past the sealed side.
  - Panels are printed outside, bare foil inside. The lining comes from
    `gl_FrontFacing` in the fragment shader, not extra meshes.
  - `packBody` is the front flap, `packLining` the far wall. They must stay
    separate: parented together, the wall rides the flap forward and crosses
    in front of the cards.
- `#site`: the page. Sections are full-bleed `.band` wrappers with a
  `.wrap` inside. Do not put horizontal padding in a `padding` shorthand on
  `.band`, `.hero` or `.foot`: they share elements with `.wrap` and will
  silently zero its side padding.
- `var CARDS` is the 80-card dataset:
  `[number, name, categoryLabel, description, hasArt]`. The board, the
  variations list and the Monochrome strip render from it.
- Section order is hero, board, rarity, variations, Monochromes, shop, art.
  The board sits directly under the hero because the owner moved it there on
  2026-08-15; it is the card list, so it leads.
- **The filterable "First Edition Checklist" grid was removed** on
  2026-08-15: the board already covers the card list. Its JavaScript is
  still in the file, guarded by `if(!grid) return;`, so it is inert but dead.
  If the grid is not coming back, that block and `.filt`, `.grid`, `.card`
  and `.detail` in the CSS can all go.

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
