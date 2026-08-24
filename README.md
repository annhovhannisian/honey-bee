# Մեղրի Տուն — Honey House

A single-page static website for a fictional Armenian family apiary, with a working
client-side shopping cart. All UI text is in Armenian.

The entire site is **one file** — `index.html`. There is no build step, no bundler,
and no dependencies to install. Markup, styles, and behaviour live in that file;
the only external resources are the Bootstrap 5 CDN, Google Fonts, and Unsplash photos.

---

## Getting started

Open `index.html` in any modern browser. That's it.

For the cart to persist across page reloads, serve the file over HTTP rather than
opening it from disk — some browsers restrict `localStorage` on `file://` origins.
The page degrades gracefully either way: without storage access the cart still works,
it just resets on reload.

Any static server will do:

```bash
npx --yes serve .
```

Then visit the URL it prints (usually `http://localhost:3000`).

---

## What's on the page

| Section | Contents |
| --- | --- |
| Navbar | Sticky, translucent, solidifies on scroll. Logo, section links with scroll-spy underline, cart button with live count badge. |
| Hero | Honeycomb-patterned dark panel, hexagon-clipped photo, two CTAs, three trust facts. |
| Ինչպես են մեղուները մեղր հավաքում | Four numbered steps with custom SVG icons — a horizontal connected timeline on desktop, a vertical spine on mobile. |
| Ո՞վ է մեղվաբույծը | Two-column portrait + text, with three stat cards (hives, years of experience, annual yield). |
| Մեր մեղրը | Six honey products — photo, name, weight, description, price in ֏, add-to-cart button. |
| Footer | Contacts, opening hours, address, social links, copyright with auto-updating year. |

### Cart

Slide-in off-canvas panel from the right:

- Add items, increase/decrease quantity, remove a line
- Live total in ֏ and a navbar badge that updates instantly
- Friendly empty state with a shortcut back to the products
- Persisted in `localStorage` under the key `meghri-tun-cart-v1`
- "Պատվիրել" clears the cart and shows a success toast — there is no backend and
  no checkout; nothing is sent anywhere

Stored data is validated on load: unknown product ids, non-positive quantities, and
malformed JSON are discarded rather than breaking the page.

---

## Design system

Everything is driven by CSS custom properties defined in `:root`, so the whole look
can be retuned from one place.

### Palette

| Variable | Value | Use |
| --- | --- | --- |
| `--honey` | `#E8A317` | Primary accent, buttons, badges |
| `--honey-deep` | `#C67C0B` | Gradient partner, hover states |
| `--amber-ink` | `#8A5A06` | Small amber text on light backgrounds |
| `--brown` | `#241A0E` | Headings, dark panels |
| `--brown-70` | `#4A3A28` | Body copy |
| `--cream` | `#FDF6E8` | Page background |
| `--cream-2` | `#F6EBD4` | Alternate section background |

`--amber-ink` exists for a reason: the brand amber `#E8A317` only reaches about 2:1
against cream, which fails WCAG. The darkened amber is used wherever small text needs
to sit on a light surface.

### Typography

- **Headings** — `Noto Serif Armenian`
- **Body** — `Noto Sans Armenian`
- **Latin numerals** (prices, stats, step numbers) — `Fraunces`

The Noto Armenian families were chosen specifically for script coverage. The display
serifs usually reached for in this kind of design (Playfair, Fraunces, Cormorant) have
no Armenian glyphs and would silently fall back to a system font.

### The honeycomb motif

One tileable hexagon SVG is defined once as a data-URI in `--hex-honey` / `--hex-brown`,
then reused as:

- a radially-masked background in the hero and footer
- a patterned block behind the beekeeper photo
- the placeholder shown if a remote photo fails to load

The same hexagon geometry also appears as `clip-path` on the hero image, the step icons,
the eyebrow bullets, and the empty-cart badge — so the motif reads as a system rather
than as wallpaper.

---

## Customizing

### Products

Product data is a single array near the top of the `<script>` block, and drives both the
grid and the cart:

```js
{
  id: 'floral',                    // unique; how a cart line refers back to the product
  name: 'Ծաղկային մեղր',
  weight: '500 գ',
  price: 3500,                     // number, in AMD
  tag: 'Հիթ',                      // optional ribbon; empty string hides it
  desc: '…',
  img: 'https://images.unsplash.com/…',
  alt: '…'                         // required for accessibility
}
```

Add or remove entries and the grid, cart, and totals follow automatically. Prices are
formatted by `fmt()`, which groups thousands with a narrow no-break space so they never
wrap mid-number.

### Colours and shape

Edit the variables in `:root`. `--r-lg` / `--r-md` / `--r-sm` control corner rounding,
`--ease` the shared easing curve, and `--nav-h` the navbar height (the hero's top padding
is derived from it, so they stay in sync).

---

## Accessibility

- Semantic landmarks: `header`, `nav`, `main`, `section`, `article`, `footer`, `address`
- Every meaningful image has an Armenian `alt`; decorative images and icons are hidden
  from assistive tech
- Cart changes are announced through a visually-hidden `aria-live` region, and the cart
  button's `aria-label` reports the current item count
- Quantity changes patch the cart row in place rather than rebuilding the list, so
  keyboard focus stays on the button being pressed. When a row genuinely disappears,
  focus is moved to a sensible neighbour instead of being dropped to `<body>`
- Skip link to the main content, and a visible honey-coloured focus ring throughout
- Scroll-reveal animations are gated behind a `.js` class, so content is never left
  invisible if scripting is unavailable
- `prefers-reduced-motion` disables reveals, floats, and smooth scrolling
- All text/background pairs were measured against WCAG AA for normal text; the lowest
  is 5.01:1

---

## Browser support

Modern evergreen browsers. The page relies on CSS custom properties, `clip-path`,
`aspect-ratio`, `backdrop-filter`, and `IntersectionObserver`. Where
`IntersectionObserver` is missing, all content is revealed immediately instead.

---

## Credits

- [Bootstrap 5](https://getbootstrap.com/) — grid, off-canvas, collapse, and toast only;
  all buttons, cards, and surfaces are custom-styled
- [Google Fonts](https://fonts.google.com/) — Noto Serif Armenian, Noto Sans Armenian, Fraunces
- [Unsplash](https://unsplash.com/) — photography, used under the Unsplash License

Content is fictional and written for demonstration purposes: the apiary, the beekeeper,
the address, and the contact details are not real.
