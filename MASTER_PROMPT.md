You are a senior front-end engineer and product designer. Build a complete, clickable, frontend-only demo of **Uni F&B by Omar Omran**: a mobile-first web app that lets university students order food and drinks from campus vendors, pay, get a receipt, and pick up the order.

The visual design is defined in the attached `DESIGN_SYSTEM.md`. Read it fully before writing any code and follow it exactly: its tokens, components, typography, iconography, voice and anti-patterns.

## 1. Product

Flow: choose university, see its vendors, open a vendor, add items to the cart, checkout and pay, then land on an order page with a receipt and a live status.

Two universities, **GIU** and **MIU**. Two vendors, **IL Container** and **Man'oucheh**. For now both vendors are available in both universities. Currency is EGP. UI language is English. Vendor and menu data is in section 9.

## 2. Hard constraints

- Frontend only. Plain HTML, CSS and minimal vanilla JavaScript. No frameworks, no build step, no npm, no backend, no external libraries, no CDN, no remote images or fonts. It must work by opening `index.html` from the file system.
- Mobile first, designed at 390px, centered 430px max-width column on larger screens.
- Pages link to each other with real `<a href>` links or `<button>` elements that navigate with `location.href`. Navigation must work without JS wherever possible.
- JS is only for: rendering data into pages, the cart (add, remove, quantity, subtotal), saving cart and orders in `localStorage`, the payment method toggle, the variant picker sheet, file upload preview, and the demo order status. Keep each script short and readable with light comments.
- No emoji, no gradients, no AI-looking decoration. Use only the colors defined in `DESIGN_SYSTEM.md`.

## 3. File structure

```
/index.html          Home: university picker and vendor list
/vendor.html         Vendor menu (reads ?v=<vendor-id>)
/checkout.html       Order summary and payment
/order.html          Receipt and order status (reads ?id=<order-number>)
/css/styles.css      Design tokens, base styles, all components
/js/data.js          Universities, vendors, menu items (from section 9)
/js/app.js           Shared helpers: cart, formatting, storage, icons
```

Every page has the same `<head>` basics (viewport with `viewport-fit=cover`, theme-color, the stylesheet) and loads `data.js` then `app.js` at the end of the body.

## 4. Pages and their structure

### 4.1 `index.html` Home

1. Large title **Uni F&B** and a small secondary line: "Order ahead on campus."
2. Section label "Your university". A grouped row with the styled university `<select>` (placeholder "Choose your university", options GIU and MIU). Selecting a university renders its vendors below without leaving the page and saves the choice in `localStorage` so it is preselected next time.
3. Section label "Vendors at <university>". Grouped list of vendor rows (monogram tile, name, cuisine and opening hours, open/closed dot, chevron). Each row links to `vendor.html?v=<vendor-id>`.
4. Empty state before a university is chosen: "Pick your university to see what's available."
5. Footer footnote: "Uni F&B by Omar Omran".

### 4.2 `vendor.html` Vendor menu

1. Nav bar with a back link to `index.html` (label: the chosen university name) and the vendor name as the small title.
2. Header block: vendor name (Title 1), cuisine, opening hours, open/closed status, pickup location (footnote). If the vendor has a `note`, show it as a footnote line.
3. Sticky category chips. Tapping a chip scrolls to that category section.
4. Category sections: section title, then a grouped list of menu item rows (name, optional description, price, image or monogram placeholder, `+` button).
   - Items with one price: `+` adds directly and turns into a quantity stepper.
   - Items with variants (size, flavor): show "From EGP <lowest price>" and tapping `+` opens a bottom sheet with the variants as radio rows (label and price) and a black "Add to cart" button. Variants of the same item are separate cart lines.
5. Sticky bottom cart bar, hidden while the cart is empty. It shows `View cart · N items` and the subtotal, and links to `checkout.html`. Subtotal updates instantly on every change.
6. The cart belongs to one vendor. If the student adds from a different vendor while the cart is not empty, clear the cart and show an info banner "Cart cleared. You can order from one vendor at a time."

### 4.3 `checkout.html` Checkout

1. Nav bar with back link to the vendor page and title "Checkout".
2. Section "Your order": vendor name, pickup location, then a grouped list of cart lines, each with name (and variant label), quantity stepper and line price. Removing the last unit removes the line. Empty cart shows the empty state with a link to `index.html`.
3. Section "Summary": subtotal, service fee (from vendor data, may be 0), total.
4. Section "Pickup details": name (required) and phone (optional).
5. Section "Payment": segmented control with **Card** and **InstaPay**.
   - **Card (Paymob):** text "Pay securely by card with Paymob." The place order button opens a simulated Paymob bottom sheet (card number, expiry, CVV as non-functional inputs, a "Pay EGP <total>" black button, and a footnote that this is a demo). Paying creates the order with payment status **Paid** and goes to the order page. Never store card data.
   - **InstaPay:** show the vendor's InstaPay handle and phone, a **reference code** block (generated like `UFB-4821`, with a Copy button), the instruction "Send EGP <total> on InstaPay and add this code in the notes", and the **proof of payment upload** area (images only, thumbnail preview, Remove). The place order button stays disabled until a file is chosen. The order is created with payment status **Awaiting confirmation**.
6. Sticky bottom bar with the primary button "Place order · EGP <total>". It validates the fields, saves the order, clears the cart and navigates to `order.html?id=<order-number>`.

### 4.4 `order.html` Order and status

1. Nav bar with title "Order" and a link to `index.html` labeled "Home". No back to checkout.
2. Top block: large check icon in a black circle, "Order placed" (Title 1), order number (e.g. `#UFB-1001`) in Title 2 tabular.
3. Status card: status pill (**Pending**, **Finished**, **Delivered**) and the vertical stepper with these descriptions:
   - Pending: "The vendor has received your order."
   - Finished: "Your order is ready. Show your order number at the counter."
   - Delivered: "Order handed over. Enjoy."
4. Pickup card: vendor name, pickup location, name for pickup.
5. Receipt block: order number, date and time, payment method, payment status, items with variant, quantity and price, subtotal, service fee, total.
6. At the bottom, a grouped block labeled "Demo only: change status" with a segmented control (Pending, Finished, Delivered) that updates the stored order and re-renders the stepper. Also support `?status=` in the URL.
7. Primary button "Order again" linking to the vendor page, and a text link "Back to home".

## 5. Data model (`js/data.js`)

```js
const UNIVERSITIES = [
  { id: "giu", name: "GIU", fullName: "German International University" },
  { id: "miu", name: "MIU", fullName: "Misr International University" }
];

// price OR variants. Items with variants have no top-level price.
const VENDORS = [
  {
    id: "il-container",
    universityIds: ["giu", "miu"],
    name: "IL Container",
    cuisine: "Burgers, chicken and drinks",
    hours: "9:00 AM to 10:00 PM",
    open: true,
    pickupLocation: "Campus food court",
    serviceFee: 0,
    note: "",
    instapay: { handle: "ilcontainer@instapay", phone: "01000000000" },
    categories: [
      {
        id: "smashed-burgers",
        name: "Smashed burgers",
        items: [
          { id: "bimbo-burger", name: "Bimbo Burger", description: "", image: null,
            variants: [{ id: "single", label: "Single", price: 145 }, { id: "double", label: "Double", price: 185 }] },
          { id: "fries", name: "French Fries", description: "", image: null, price: 45 }
        ]
      }
    ]
  }
];
```

Cart in `localStorage` key `ufb_cart`: `{ vendorId, lines: { "<itemId>:<variantId or base>": qty } }`. Orders in `ufb_orders` keyed by order number. Chosen university in `ufb_university`. Order numbers start at 1001 and display as `#UFB-1001`. Money helper outputs `EGP 145`.

## 6. Behavior details

- If a page is opened without required params or data, redirect to `index.html`.
- Keyboard and screen reader friendly: real buttons, labels on inputs, `aria-label` on icon buttons, visible `:focus-visible` ring. Respect `prefers-reduced-motion`.
- All pages are linked: index to vendor to checkout to order, back links as described, "Order again" closes the loop. No dead links and no `#` placeholders.
- Items have no photos. Use the monogram or line-icon placeholder tiles from the design system. Do not leave empty image boxes.

## 7. Quality bar

- Looks like a native iOS app: large titles, grouped inset lists, hairline separators, bottom sheets, segmented control, sticky action bar, system font.
- Black and white dominate. Green, yellow, blue and red appear only in the roles the design system defines.
- Consistent spacing from the 4/8 scale, no overflow, no horizontal scroll at 360px width. Long menus (IL Container has about 100 items) must stay fast and easy to scan with the sticky category chips.
- Clean code, no unused CSS or JS, no console errors.

## 8. Output

Generate every file in full with its path as a heading, in this order: `css/styles.css`, `js/data.js`, `js/app.js`, `index.html`, `vendor.html`, `checkout.html`, `order.html`. Then give a 3-line note on how to run it (open `index.html`) and what is simulated (Paymob sheet, InstaPay confirmation, status changes). Do not add features beyond this spec. Convert section 9 into `data.js` exactly: keep every item name and price as written, do not rename, merge or drop items, and do not invent extra items.

## 9. Vendor data

Both vendors are available in both universities (GIU and MIU). Prices in EGP.

### Vendor 1: IL Container

- id: `il-container`
- Universities: GIU, MIU
- Cuisine: Burgers, chicken and drinks
- Hours: 9:00 AM to 10:00 PM, open now
- Pickup location: Campus food court
- Service fee: 0
- InstaPay: handle `ilcontainer@instapay`, phone `01000000000` (placeholders)
- Note shown on the vendor page: "All sandwiches are served with fries."

Format: Item | price, or Item | Variant price / Variant price.

**Smashed burgers** (variants Single / Double)
- Bimbo Burger | Single 145 / Double 185
- Cheese Burger | Single 150 / Double 190
- Volcano | Single 155 / Double 195
- Mushroom Burger | Single 155 / Double 195
- Truffle Burger | Single 165 / Double 205

**Fried chicken** (variants Single / Double)
- Original Big Bimbo | Single 135 / Double 180
- Rancheister | Single 145 / Double 185
- Volcano | Single 150 / Double 190
- Buffalo Chicken Ranch | Single 155 / Double 195

**Boxes**
- Mesa7ab Box | 185
- Tender Box | 225

**Bimbo fries**
- French Fries | 45
- Cheesy Fries | 65
- Crispy Chicken Fries | 115
- Mexican Hotdog Fries | 120
- Smashy Fries | 120
- Truffle Mushroom Fries | 145

**Frappe and milkshakes**
- Frappuccino | 100
- Caramel Frappe | 120
- Lotus Frappe | 135
- Pistachio Frappe | 140
- Classic Milkshake | Vanilla 100 / Chocolate 100 / Caramel 100
- Fruit Milkshake | Strawberry 130 / Peach 130 / Mango 130
- Oreo Milkshake | 130
- Lotus Milkshake | 135
- Pistachio Milkshake | 140

**Boba craze**
- Brown Sugar BOBA | 120
- Classic Milk Tea BOBA | 120
- Peach Ice Tea BOBA | 125
- Mango Coconut BOBA | 125
- Brown Sugar Matcha BOBA | 145
- Strawberry Popping Matcha | 165
- Salted Caramel Latte BOBA | 165

**Bimbo Japanese matcha**
- Original Matcha Latte | 115
- Mango Matcha | 130
- Strawberry Matcha | 135
- Cherry Matcha | 135
- Spanish Matcha | 150
- White Chocolate Matcha | 145
- Salted Caramel Matcha Cold Foam | 165
- Pink Matcha Cold Foam | 165
- Coconut Cream Matcha | 175

**Coffee drinks**
- Espresso | 50
- Double Espresso | 70
- Americano | 75
- Cappuccino | 95
- Flat White | 95
- Cortado | 95
- Macchiato | 95
- Latte | 95
- Spanish Latte | 110
- Caramel Latte | 110
- Salted Caramel Latte | 130
- Vanilla Latte | 110
- White Mocha | 120
- Dark Mocha | 120
- Cookies White Mocha | 130
- Pistachio Latte | 140

**Refreshing drinks**
- Mohito | Peach 85 / Passion 85 / Berry 85
- Pinacolada Mohito | 95
- Blue Passion Mohito | 95
- Pink Lemonade | 95
- Ice Tea | Peach 90 / Passion 90 / Berries 90
- Karkadeh Passion Fruit | 90
- Mango Coco Lemon | 100
- Strawberry DIVA | 120
- Peach Passion Smoothie | 120
- Pinacolada Smoothie | 120
- Dirty Lemon Mint Smoothie | 100

**Non coffee**
- Black Tea | 25
- Green Tea | 25
- Karak Tea | 65
- Bimbo Hot Chocolate | 85
- Pistachio Hot Chocolate | 95
- White Hot Chocolate | 95
- Hot Pistachio | 105
- Hot Lotus | 95

### Vendor 2: Man'oucheh

- id: `manoucheh`
- Universities: GIU, MIU
- Cuisine: Lebanese manakish and pizza
- Hours: 8:00 AM to 10:00 PM, open now
- Pickup location: Campus food court
- Service fee: 0
- InstaPay: handle `manoucheh@instapay`, phone `01000000000` (placeholders)
- Note: none
- Demo menu: items follow the manakish, shawerma, pizza and sweet manakish categories the restaurant is known for. Prices are demo estimates.

**Zaatar and cheese manakish**
- Zaatar Man'oucheh | 45
- Zaatar and Cheese | 65
- Akkawi Cheese | 70
- Mixed Cheese | 85
- Halloumi Cheese | 95
- Spinach | 60

**Meat manakish**
- Lahmeh Bi Ajine | 90
- Meat and Cheese | 110
- Sausage and Cheese | 100
- Chicken and Cheese | 100

**Omelette manakish**
- Cheese Omelette | 70
- Sausage Omelette | 85

**Shawerma**
- Chicken Shawerma | 90
- Beef Shawerma | 115
- Mixed Shawerma | 120

**Pizza**
- Margherita | 95
- Mixed Cheese Pizza | 120
- Pepperoni Pizza | 125
- Chicken Pizza | 125

**Sweet manakish**
- Chocolate and Banana | 70
- Nutella | 75

**Drinks**
- Water | 10
- Soft Drink | 25
- Ayran | 30
- Fresh Orange Juice | 50
