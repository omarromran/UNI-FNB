# Uni F&B — Design System

Mobile-first web app. Native-iOS feel: calm, flat, high-contrast, generous whitespace. Black and white carry the interface; color is reserved for meaning.

## 1. Principles

1. **Black and white first.** Surfaces are white or light gray, text and primary buttons are black. Color appears only for status and feedback.
2. **Content over chrome.** No decorative gradients, glows, blobs, glass-heavy cards or illustrations. Let the menu items and prices be the design.
3. **One primary action per screen.** It is a full-width black button, pinned to the bottom, thumb-reachable.
4. **iOS conventions.** Large titles, grouped inset lists, bottom sheets, segmented controls, hairline separators, 44px minimum tap targets.
5. **Quiet motion.** Short, functional transitions only (150–250ms). No bouncing, no parallax, no confetti.

## 2. Color tokens

```css
:root {
  /* Core */
  --black: #000000;
  --white: #FFFFFF;

  /* Neutrals (iOS system grays) */
  --bg: #F2F2F7;            /* app background, behind grouped lists */
  --surface: #FFFFFF;       /* cards, grouped rows */
  --surface-2: #F2F2F7;     /* inputs, chips, image placeholders */
  --separator: rgba(60, 60, 67, 0.18);   /* hairline */
  --text: #000000;
  --text-2: rgba(60, 60, 67, 0.60);      /* secondary text */
  --text-3: rgba(60, 60, 67, 0.30);      /* disabled, placeholder */

  /* Secondary colors: used only with meaning */
  --green:  #34C759;  --green-bg:  #E8F8ED;   /* success, Finished */
  --yellow: #FFCC00;  --yellow-bg: #FFF7D6;   /* attention, Pending */
  --blue:   #007AFF;  --blue-bg:   #E5F1FF;   /* info, links, Delivered */
  --red:    #FF3B30;  --red-bg:    #FFE9E7;   /* error, remove */
}
```

**Color roles (strict)**

| Color | Allowed use |
|---|---|
| Black | Primary buttons, titles, active states, selected radio/segment |
| Green | Status "Finished", success check, "Paid" |
| Yellow | Status "Pending", payment awaiting confirmation |
| Blue | Status "Delivered", text links, focus ring, info banners |
| Red | Errors, remove/delete, destructive actions |

Never use color as a large fill for a whole section. Status colors appear as small pills, dots or icons with the matching `-bg` tint behind dark text. Maximum one accent color visible per component.

## 3. Typography

System font only. This is what makes it feel native.

```css
font-family: -apple-system, BlinkMacSystemFont, "SF Pro Text", "SF Pro Display",
             "Inter", "Segoe UI", Roboto, system-ui, sans-serif;
-webkit-font-smoothing: antialiased;
```

| Role | Size / Line | Weight | Tracking |
|---|---|---|---|
| Large title | 34 / 41 | 700 | -0.4px |
| Title 1 | 28 / 34 | 700 | -0.3px |
| Title 2 | 22 / 28 | 600 | -0.2px |
| Headline | 17 / 22 | 600 | -0.4px |
| Body | 17 / 22 | 400 | -0.4px |
| Callout | 16 / 21 | 400 | -0.3px |
| Subhead | 15 / 20 | 400 | -0.2px |
| Footnote | 13 / 18 | 400 | -0.1px |
| Caption | 12 / 16 | 500 | 0 |

Rules: sentence case everywhere (no ALL CAPS except tiny section labels at 13px, letter-spacing 0.4px, secondary color). Prices use `font-variant-numeric: tabular-nums`. Maximum two font weights per screen section.

## 4. Spacing, radius, elevation

- **Spacing scale (px):** 4, 8, 12, 16, 20, 24, 32, 40. Screen side padding is 16px.
- **Radius:** 10px (inputs, small buttons), 14px (buttons, list groups), 20px (cards, sheets top corners), 999px (pills, chips).
- **Elevation:** almost none. Cards are white on the gray `--bg` with no shadow. Only floating bars and sheets get a soft shadow: `0 -1px 0 var(--separator)` for bars, `0 -8px 32px rgba(0,0,0,0.12)` for sheets.
- **Borders:** 0.5px hairlines using `--separator`. Prefer separators between rows over boxing every item.

## 5. Layout

- Mobile first: design at 390px width. Content is a single column.
- On screens wider than 480px, center the app in a 430px max-width column with `--bg` outside, so it still reads as a phone-style web app.
- Respect safe areas: `padding-bottom: env(safe-area-inset-bottom)` on pinned bars, `padding-top: env(safe-area-inset-top)` on the header.
- Page background is `--bg`. Content lives in white grouped blocks (inset lists) with 16px margin.
- Viewport: `<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">`. Set `<meta name="theme-color" content="#F2F2F7">`.

## 6. Components

### Navigation bar
Sticky top, 44px content height, translucent `--bg` with `backdrop-filter: blur(20px)` (the one allowed use of blur), hairline bottom border appearing on scroll. Left: back chevron + label in blue. Center: small title. Right: optional action. Large title sits below the bar on top-level pages.

### Primary button
Height 52px, full width, radius 14px, background black, text white, 17px/600. Pressed: opacity 0.85. Disabled: background `--surface-2`, text `--text-3`.

### Secondary button
Same size, background `--surface-2`, text black. Tertiary: text-only, blue.

### Sticky action bar (cart bar / checkout bar)
Fixed to bottom, white, hairline top border, 16px padding plus safe-area. Contains one primary button. In the cart bar the button label is `View cart · 3 items` with the subtotal right-aligned inside the button (e.g. `EGP 145`).

### Grouped list (inset)
White container, radius 14px, rows 52–64px, 0.5px separators inset 16px from the left, no outer border. Section label above in footnote style, secondary color.

### University select
A native-feeling field: label above, 52px tall row in a grouped block, value on the left, chevron-up-down on the right. Use a real `<select>` styled with `appearance: none` so the iOS picker wheel appears on phones.

### Vendor row
Leading 56px rounded-14 monogram tile (`--surface-2` with the vendor's initials, 17px/600), then name (headline), then one line of secondary text (cuisine · opening hours), trailing chevron. Open/closed is a small dot plus text: green dot "Open", red dot "Closed".

### Menu item row
Left: name (body/600), description (footnote, secondary, max 2 lines), price (subhead, tabular). Right: 88px square rounded-14 image or monogram placeholder with a 32px circular `+` button overlapping its bottom-right corner (white, hairline border, black plus). After adding, the `+` becomes a quantity stepper (`−  2  +`) in a white pill.

### Category chips
Horizontal scroll row, no scrollbar, 36px tall pills. Inactive: `--surface-2` with black text. Active: black with white text. Sticky under the nav bar.

### Segmented control
Used for payment method choice (`Card` / `InstaPay`). 32px high, `--surface-2` track, white thumb with a soft shadow, radius 9px.

### Status pill
Height 24px, radius 999px, 12px/600 text, dot 6px + label. Pending = yellow-bg with dark text and yellow dot. Finished = green-bg with green dot. Delivered = blue-bg with blue dot.

### Status stepper (order page)
Vertical, three steps (Pending → Finished → Delivered), 24px circle markers connected by a 2px line. Completed steps: black filled circle with white check. Current step: black ring with the matching status color dot inside. Upcoming: `--separator` outline. Each step has a title (headline) and a one-line description (footnote, secondary).

### Receipt block
White card, radius 20px, dashed hairline separator between header and items (a single 1px dashed `--separator`). Monospace is not used. Items as two-column rows (name x qty on the left, price on the right), then subtotal, service fee (if any), total in headline weight. Large order number on top (Title 1, tabular).

### File upload (InstaPay proof)
Dashed 1.5px `--separator` rounded-14 drop area, 120px tall, centered icon and text "Upload payment screenshot". Once chosen: show a 56px thumbnail, file name, and a red "Remove" text button. Use `<input type="file" accept="image/*">` hidden behind a `<label>`.

### Reference code block
Surface-2 rounded-14 block with the code in Title 2 tabular, and a "Copy" blue text button on the right. Under it, footnote: "Add this code in the InstaPay transfer notes."

### Bottom sheet (Paymob demo)
Slides up from the bottom, radius 20px top corners, grabber 36x5px `--text-3` centered at the top, dimmed overlay `rgba(0,0,0,0.4)`. Used for the simulated Paymob payment step.

### Banner
Rounded-14, tinted `-bg` color, 16px padding, icon + text. Info = blue, warning = yellow, error = red. Text color stays near-black, not the accent color.

### Empty state
Centered, one line title (headline), one line secondary (subhead), optional text button. No illustrations.

## 7. Iconography

Inline SVG, 1.75px stroke, rounded caps and joins, 24px grid, `currentColor`. Style matches SF Symbols: chevron.left, chevron.right, plus, minus, bag, checkmark, clock, doc.text, photo, xmark, creditcard, building.2. No emoji anywhere in the UI. No icon libraries loaded from a CDN. Write the SVGs inline or as an `icons.svg` sprite.

## 8. Imagery

No stock photos and no remote images. If a vendor or item has no image, use a `--surface-2` tile with a monogram (vendor) or a simple food/drink line icon (item) in `--text-3`. If the user supplies image paths, show them with `object-fit: cover`, radius 14px, and a 0.5px inner border.

## 9. Interaction and motion

- Tap feedback: `:active` opacity 0.85 or background darkens to `rgba(0,0,0,0.04)` on rows. No hover-only behavior.
- Transitions: 200ms `cubic-bezier(0.2, 0.8, 0.2, 1)` for sheets and steppers.
- Focus: 2px blue outline offset 2px for keyboard users only (`:focus-visible`).
- Tap targets are at least 44x44px. Text inputs use 16px font size or larger to prevent iOS zoom.
- Prevent double-tap zoom with `touch-action: manipulation`.

## 10. Voice and content

- Short, plain, human. "Order placed", not "Your order has been successfully submitted!".
- Currency format: `EGP 85` (no decimals unless needed), tabular numerals.
- Button labels are verbs: "View cart", "Continue to payment", "Place order", "Track order".
- No exclamation marks, no marketing copy, no emoji.

## 11. Accessibility

- Text contrast at least 4.5:1. Never rely on color alone: every status pill has a text label.
- All icons that act as buttons have `aria-label`.
- Semantic HTML: `<header>`, `<main>`, `<nav>`, `<button>`, `<label>`, lists as `<ul>`.
- Respect `prefers-reduced-motion` by disabling transitions.

## 12. Anti-patterns (do not do)

- No purple or blue-purple gradients, neon glows, or glassmorphism cards stacked on gradients.
- No emoji as icons, no sparkles, no "AI" styling.
- No more than one accent color per component, and never color as decoration.
- No heavy drop shadows, no thick borders, no colored card backgrounds.
- No centered hero marketing sections, taglines in oversized gradient text, or fake testimonials.
- No Bootstrap or Tailwind default look. Everything follows the tokens above.
- No horizontal scrolling of the page body, no tiny text under 12px.
