# Shopify Sections

Three drop-in sections for Shopify Online Store 2.0 themes. Each section is a single, self-contained `.liquid` file: markup, scoped CSS, schema and (where needed) a small amount of vanilla JavaScript all live in one place, so there is nothing else to install.

| Section | File | JavaScript |
| --- | --- | --- |
| Trust badges bar | `trust-badges-bar.liquid` | None |
| FAQ accordion with FAQPage schema | `faq-accordion.liquid` | None |
| Sticky add to cart | `sticky-add-to-cart.liquid` | Vanilla, one custom element |

## Design principles

- **Self-contained.** No snippets, assets, apps or theme edits required.
- **Scoped styles.** Every CSS rule is prefixed with a class that includes `section.id`, so sections never leak styles into the theme and can be added more than once per page.
- **Merchant-editable.** Text, colors, spacing and layout are exposed as theme editor settings and blocks. Nothing is hard-coded.
- **Accessible.** Semantic markup, visible focus states, decorative icons hidden from assistive technology, live regions for status messages, and respect for `prefers-reduced-motion`.
- **Light.** No jQuery, no external libraries, lazy-loaded images with explicit `width` and `height` to avoid layout shift.

## Installation

1. In your Shopify admin go to **Online Store > Themes**. Duplicate your live theme first so you have a backup.
2. On the theme, click **... > Edit code**.
3. Under **Sections**, click **Add a new section**, choose **Liquid**, and give it the same name as the file (for example `faq-accordion`).
4. Replace the generated content with the content of the file from this repo and click **Save**.
5. Open the theme editor (**Customize**), click **Add section**, and pick the section by name.

If you work locally with Shopify CLI, copy the files into your theme's `sections/` folder and run `shopify theme dev`.

## Sections

### 1. Trust badges bar

A responsive row of icon and text items, for messages such as shipping, returns, secure checkout and support.

- Each item is a block (up to 8), so merchants can add, remove and reorder items.
- Eight built-in inline SVG icons, or upload a custom icon image per item.
- Renders as a semantic list with a screen reader label.

| Setting | Description |
| --- | --- |
| Item layout | Icon beside text, or icon above text |
| Columns on desktop / mobile | 2 to 6 on desktop, 1 or 2 on mobile |
| Icon size | 20 to 64 px |
| Content max width, top and bottom padding | Layout controls |
| Background, text and icon colors | Color pickers |
| Screen reader label | Accessible name for the list |
| Block: icon, custom icon image, title, supporting text | Per-item content |

### 2. FAQ accordion

Question and answer blocks built on native `<details>` and `<summary>` elements. The accordion works with the keyboard and screen readers out of the box and needs no JavaScript.

- Outputs valid `FAQPage` JSON-LD generated from the same blocks that render on the page, so the visible content and the structured data always match.
- Answers are rich text (links, lists, bold). The structured data uses a plain-text version, and all values pass through Liquid's `json` filter so merchant text cannot break the JSON.
- Optional "only one question open at a time" mode uses the native `name` attribute on `<details>`. Browsers that do not support it fall back to allowing several open items.

| Setting | Description |
| --- | --- |
| Heading, heading size, alignment, intro text | Section header content |
| Open first question on page load | On or off |
| Only one question open at a time | On or off |
| Output FAQ structured data (JSON-LD) | On or off. Enable on one FAQ section per page |
| Content max width, top and bottom padding | Layout controls |
| Background, text, divider and accent colors | Color pickers |
| Block: question, answer | Up to 30 questions |

Note: structured data makes a page eligible for rich results, but search engines decide whether to show them.

### 3. Sticky add to cart

A bar fixed to the bottom of the product page. It appears after the theme's main add to cart button has scrolled out of view and hides again when the button is back on screen.

- Uses `IntersectionObserver`, so there are no scroll listeners.
- Variant dropdown (or a hidden input for single-variant products) with live price, compare-at price and sold-out state.
- Picks up the variant currently selected in the main product form each time the bar appears.
- Adds to cart with the Shopify Ajax Cart API (`/cart/add.js`), using `routes.cart_add_url` so it works with locale-aware URLs.
- Shows Shopify's own error message when an item cannot be added (for example, not enough stock).
- While hidden, the bar is `inert`, so keyboard and screen reader users never land on off-screen controls.
- Can only be added to product templates (`enabled_on` in the schema).

| Setting | Description |
| --- | --- |
| Show on | All devices, mobile only or desktop only |
| Show product image | On or off |
| After adding to cart | Stay on page and confirm, or go to the cart page |
| Main add to cart button selector | CSS selector of the theme's main buy button. Defaults to Dawn's `.product-form__submit`, with a generic product form fallback |
| Button, sold out and confirmation labels | Editable text |
| Bar and button colors | Color pickers |

**Cart drawer integration.** After a successful add, the section updates Dawn's cart icon count and dispatches a bubbling `sticky-atc:added` event. Cart drawers differ between themes, so opening one is left to a short listener:

```js
document.addEventListener('sticky-atc:added', (event) => {
  // event.detail is the /cart/add.js response
  // Open or refresh your theme's cart drawer here.
});
```

## Compatibility

- Built for Online Store 2.0 themes (JSON templates and sections everywhere), such as Dawn and other themes with a similar structure.
- The sections do not depend on any theme CSS or JavaScript. Two optional touches use Dawn conventions when present: the heading size classes (`h0`, `h1`, `h2`) and the `cart-icon-bubble` section for the cart count.
- Modern evergreen browsers. `IntersectionObserver`, custom elements and `inert` are required for the sticky bar's full behavior.
- Always test on a duplicate theme before publishing.

## License

MIT. See [LICENSE](LICENSE). The built-in icons in the trust badges bar use paths from [Feather Icons](https://feathericons.com), also MIT licensed.

## Need this customized?

These sections are a starting point. If you want them matched to your theme, wired into your cart drawer, or extended with new settings, get in touch.

**Usama Afzal**, Shopify developer and founder of [Website Prism](https://websiteprism.com)

- Contact: https://usama.websiteprism.com/contact
- Portfolio: https://usama.websiteprism.com
- Email: usama@websiteprism.com
