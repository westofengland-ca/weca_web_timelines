# Timelines

Timelines for embedding on Mayoral Combined Authority websites. Each timeline is a single HTML file hosted on GitHub Pages and placed on a WordPress page with an iframe.

Use this repo for any new timeline: copy an existing file, change the dates and content, and embed it.

Repo: https://github.com/westofengland-ca/weca_web_timelines

Base URL: https://westofengland-ca.github.io/weca_web_timelines/

Each timeline is published at the base URL followed by its file name, for example https://westofengland-ca.github.io/weca_web_timelines/SDS-timeline.html

## Timelines in this repo

| File | Content | Used on |
| --- | --- | --- |
| `SDS-timeline.html` | Spatial Development Strategy (SDS) stages, 2025 to 2029 | westofengland-ca.gov.uk, Vision 2060 page |
| `sds-timeline-v2.html` | SDS stages with a details panel for each stage, labelled statutory or non-statutory. Evenly spaced, not to scale | In review |
| `index.html` | Placeholder content, 2026 to 2060. Switches to a vertical list below 720px | Not used |

Add a row when you publish a new timeline, so anyone changing a file knows which page it affects.

Each file is self-contained: HTML, CSS and JavaScript are in one file. There is no build step and nothing to install.

## Making a new timeline

1. Copy `SDS-timeline.html` and give it a short name with no spaces, for example `bus-franchising.html`
2. Update the `<title>` and the hidden `<h2>` with the timeline's name and date range
3. Set the date range and year markers (see [Setting the date range](#setting-the-date-range))
4. Replace the stages (see [Adding stages](#adding-stages))
5. Commit, wait for GitHub Pages to update, and check the page at desktop and phone widths
6. Measure the height and embed it (see [Embedding in WordPress](#embedding-in-wordpress))
7. Add it to the table above

Use `SDS-timeline.html` as the starting point. It stays horizontal and scrolls sideways on narrow screens. Use `index.html` instead if you want the timeline to turn into a vertical list on phones, which is better for accessibility (see [Accessibility](#accessibility)).

## Setting the date range

Positions along the line are percentages, from 0 at the start date to 100 at the end date.

`--pos = (date - start) / (end - start) x 100`

Use decimal years for dates within a year:

| Point in the year | Add to the year |
| --- | --- |
| Start of January | 0 |
| Spring | 0.25 |
| Summer | 0.5 |
| Autumn | 0.8 |
| Winter | 0.9 (late in that calendar year) |

"Winter" is ambiguous. Check with whoever supplied the content whether they mean the start or the end of the year.

Example: for a range from 2025 to 2030, autumn 2027 is (2027.8 - 2025) / 5 x 100 = 56.

Start and end on 1 January, so the year markers fall on round numbers. A 5-year range has a marker every 20; a 10-year range has one every 10.

Update:

1. The year markers in `<ul class="tl-ticks">`, evenly spaced from 0 to 100
2. The formula in the HTML comment above `<ol class="tl-items">`, so the next person can follow it

Keep the range close to the content. If most stages fall in 5 years and one is 30 years later, a proportional line squashes everything into the first few centimetres. Put a distant end date in the page text instead.

## Adding stages

Each stage is an `<li class="tl-item">` inside `<ol class="tl-items">`:

```html
<li class="tl-item is-above" style="--pos: 38">
  <span class="tl-dot" aria-hidden="true"></span>
  <span class="tl-stem" aria-hidden="true"></span>
  <div class="tl-card">
    <span class="tl-year">Stage 2 - winter 2026</span>
    <h3 class="tl-label">Initial public engagement</h3>
    <p class="tl-sub">On a vision for the SDS and 'call for big ideas'</p>
  </div>
</li>
```

Card content:

- `tl-year` - the date, and stage number if there is one
- `tl-label` - the heading, kept short
- `tl-sub` - optional supporting text, around 15 words at most
- `tl-list` - optional bulleted list (a `<ul class="tl-list">`), lowercase with no full stops

Expandable detail, for lists or longer text that would make the card too tall:

```html
<details class="tl-more">
  <summary>What this includes</summary>
  <ul class="tl-list">
    <li>first item</li>
    <li>second item</li>
  </ul>
</details>
```

The panel opens downwards over the space below the card, so opening it doesn't change the height of the timeline or the iframe. Put a card with expandable detail above the line, with no card close to it below the line. The script reserves enough space below the line for the open panel. Escape closes it.

Card with an image:

```html
<div class="tl-card tl-card--image">
  <img src="images/example.jpg" alt="Describe the image" width="380" height="220">
  <div class="tl-body">
    <span class="tl-year">2027</span>
    <h3 class="tl-label">Heading</h3>
  </div>
</div>
```

Put images in an `images` folder and keep them under about 100KB. Leave `alt=""` only if the image is decorative.

### Avoiding overlaps

Put each stage `is-above` or `is-below` the line. Cards on the same side must be at least 20 apart in `--pos`, or they overlap on phones. Put stages close together in time on opposite sides.

If there are too many stages for that, raise the track's `min-width` in the CSS (1000px on larger screens, 800px at 720px or less). The minimum gap is then 150 / phone min-width x 100. For example, a phone min-width of 1000px allows stages 15 apart. The cost is more sideways scrolling.

The alternative is to space stages evenly instead of by date. The line then no longer shows real gaps in time, so say so in the page text.

Cards near either end are moved inwards automatically, so they don't need special classes.

## Embedding in WordPress

Paste this into a Custom HTML block, changing the file name, `title` and height:

```html
<iframe class="mca-timeline"
  src="https://westofengland-ca.github.io/weca_web_timelines/SDS-timeline.html"
  title="Spatial Development Strategy timeline"
  loading="lazy"
  style="display:block;width:100%;border:0;height:610px"></iframe>
<script>
  window.addEventListener("message", function (e) {
    if (e.origin !== "https://westofengland-ca.github.io") return;
    if (!e.data || e.data.type !== "mca-timeline-height") return;
    document.querySelectorAll("iframe.mca-timeline").forEach(function (frame) {
      if (frame.contentWindow === e.source) frame.style.height = e.data.height + "px";
    });
  });
</script>
```

The script resizes each iframe to fit its timeline. It matches messages to the iframe that sent them, so it works with more than one timeline on a page. Use the script once per page.

The `title` is what screen readers announce for the iframe. Make it describe that timeline.

### When WordPress strips the script

WordPress removes scripts for users without the `unfiltered_html` capability. To check, view the page source (Ctrl+U) and search for `mca-timeline-height`. If it isn't there, the script was stripped and the iframe stays at the fixed height in its `style` attribute.

The fixed height has to fit the tallest layout, or a vertical scrollbar appears inside the iframe. To measure it:

1. Open the timeline's GitHub Pages URL
2. Open dev tools and check the height of `section.tl` at about 1200px, 900px and 400px wide
3. Use the largest figure as the iframe height

On phones this usually leaves some blank space under the timeline. That is the cost of not having the script.

Measure again whenever the content changes. Longer text makes cards taller.

## How the layout works

The script at the bottom of each file:

- sets the space above and below the line from the tallest card on each side, so there is no empty band
- moves cards near either end inwards so they stay inside the track
- shows "Scroll sideways to see the full timeline" only when the timeline is wider than the space
- sends the timeline's height to the parent page for the iframe resize

If the script fails, the CSS falls back to fixed heights, which may leave some extra space.

## Accessibility

- the scrolling area can be focused with the keyboard and scrolled with the arrow keys
- year markers, dots and stems are hidden from screen readers
- stages are an ordered list, each with an `<h3>`, under a hidden `<h2>`
- abbreviations like "SDS" should be spelled out in the page text around the embed

WCAG 2.2 success criterion 1.4.10 (Reflow) requires content to work at 320px wide without scrolling in two directions. A timeline may fall under the exception for content that needs a two-dimensional layout, but this is arguable. If an audit flags a landscape timeline, rebuild it from `index.html`, which reflows to a vertical list.

## Brand

Colours and fonts follow the Mayoral Combined Authority brand, set as CSS custom properties at the top of each file. Headings use the AvenirPro fonts from the Typekit kit `lkt5fce`. If the kit is locked to the WordPress domains, it will not load on github.io and headings fall back to Open Sans.

## Publishing changes

Commit to the default branch. GitHub Pages usually updates within a few minutes. Do a hard refresh (Ctrl+Shift+R) on the WordPress page to see the change.

The origin check in the embed script is `https://westofengland-ca.github.io`, with no path and no trailing slash. It stays the same for every timeline in this repo.

Don't rename a file once it is embedded. The iframe `src` depends on the exact file name, and renaming breaks every page using it. Keep file names free of spaces and download suffixes such as `(1)`.

Everything in this repo is public, including draft content and commit history. Don't commit content before it is cleared for publication.
