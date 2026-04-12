# HTML & CSS Interview Topics: Basic to Advanced

---

## TABLE OF CONTENTS

### HTML
1. [HTML Basics](#1-html-basics)
2. [HTML Forms](#2-html-forms)
3. [HTML5 Semantic Elements](#3-html5-semantic-elements)
4. [HTML Accessibility (a11y)](#4-html-accessibility-a11y)
5. [HTML Media & Embedding](#5-html-media--embedding)
6. [HTML APIs & Advanced Features](#6-html-apis--advanced-features)

### CSS
7. [CSS Basics](#7-css-basics)
8. [CSS Box Model](#8-css-box-model)
9. [CSS Layouts](#9-css-layouts)
10. [CSS Flexbox](#10-css-flexbox)
11. [CSS Grid](#11-css-grid)
12. [CSS Positioning](#12-css-positioning)
13. [CSS Typography](#13-css-typography)
14. [CSS Colors & Backgrounds](#14-css-colors--backgrounds)
15. [CSS Transitions & Animations](#15-css-transitions--animations)
16. [CSS Responsive Design](#16-css-responsive-design)
17. [CSS Variables (Custom Properties)](#17-css-variables-custom-properties)
18. [CSS Specificity & Cascade](#18-css-specificity--cascade)
19. [CSS Preprocessors](#19-css-preprocessors)
20. [CSS Advanced Topics](#20-css-advanced-topics)
21. [Quick Reference: Common Interview Questions](#21-quick-reference-common-interview-questions)

---

# HTML

---

## 1. HTML BASICS

---

### 1.1 What is HTML?

HTML (HyperText Markup Language) is the standard markup language used to create the structure of web pages. It describes content using **elements** represented by **tags**.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Page Title</title>
  </head>
  <body>
    <h1>Hello World</h1>
  </body>
</html>
```

**`<!DOCTYPE html>`** — Declares the document type and version (HTML5). Must be the first line.  
**`<html lang="en">`** — Root element; `lang` helps screen readers and search engines.  
**`<head>`** — Contains metadata (not visible on page).  
**`<body>`** — Contains everything visible on the page.

---

### 1.2 Block vs Inline Elements

**Block-level elements:**
- Start on a new line
- Take full width available
- Can contain inline and other block elements
- Examples: `<div>`, `<p>`, `<h1>`–`<h6>`, `<ul>`, `<ol>`, `<li>`, `<table>`, `<section>`, `<article>`

**Inline elements:**
- Do NOT start on a new line
- Take only as much width as necessary
- Should only contain other inline elements (not block)
- Examples: `<span>`, `<a>`, `<strong>`, `<em>`, `<img>`, `<input>`, `<label>`, `<button>`

**Inline-block:**
- Behaves like inline (sits on same line) but respects width/height like block
- Example: `<img>`, elements with `display: inline-block`

```html
<p>This is a <strong>block</strong> paragraph with <span>inline span</span>.</p>
```

---

### 1.3 Common HTML Tags

**Headings:**
```html
<h1>Most Important</h1>
<h2>Section Title</h2>
<!-- h1 to h6 -->
```

**Text:**
```html
<p>Paragraph</p>
<strong>Bold (semantic importance)</strong>
<b>Bold (visual only)</b>
<em>Italic (emphasis)</em>
<i>Italic (visual only)</i>
<u>Underline</u>
<s>Strikethrough</s>
<mark>Highlighted</mark>
<small>Smaller text</small>
<sup>Superscript</sup> and <sub>Subscript</sub>
<code>Inline code</code>
<pre>Preformatted text</pre>
<blockquote>Long quotation</blockquote>
<q>Short inline quote</q>
<abbr title="HyperText Markup Language">HTML</abbr>
<br /> <!-- Line break -->
<hr /> <!-- Horizontal rule -->
```

**Lists:**
```html
<!-- Unordered -->
<ul>
  <li>Item 1</li>
  <li>Item 2</li>
</ul>

<!-- Ordered -->
<ol type="1" start="1">
  <li>First</li>
  <li>Second</li>
</ol>

<!-- Description list -->
<dl>
  <dt>HTML</dt>
  <dd>HyperText Markup Language</dd>
</dl>
```

**Links:**
```html
<a href="https://example.com" target="_blank" rel="noopener noreferrer">
  External Link
</a>
<a href="#section-id">Jump to Section</a>
<a href="mailto:hi@example.com">Send Email</a>
<a href="tel:+919999999999">Call Us</a>
```

**`target="_blank"`** opens in a new tab. Always add `rel="noopener noreferrer"` for security (prevents the new tab from accessing the opener page via `window.opener`).

**Images:**
```html
<img
  src="photo.jpg"
  alt="Description of the image"
  width="300"
  height="200"
  loading="lazy"
/>
```

`alt` — Required for accessibility and SEO. Describes image for screen readers and when image fails to load.  
`loading="lazy"` — Defers loading until image is near the viewport (performance).

---

### 1.4 HTML Attributes

Attributes provide additional information about elements and are placed inside the opening tag.

```html
<element attribute="value">Content</element>
```

**Global Attributes (work on any element):**

| Attribute | Purpose |
|---|---|
| `id` | Unique identifier for an element |
| `class` | One or more CSS class names |
| `style` | Inline CSS styles |
| `title` | Tooltip text shown on hover |
| `lang` | Language of the element's content |
| `tabindex` | Tab order for keyboard navigation |
| `hidden` | Hides element from rendering |
| `data-*` | Custom data attributes |
| `contenteditable` | Makes element editable |
| `draggable` | Makes element draggable |
| `aria-*` | Accessibility attributes |

**Custom Data Attributes:**
```html
<button data-user-id="42" data-role="admin">Click Me</button>
```
```js
const btn = document.querySelector("button");
btn.dataset.userId; // "42"
btn.dataset.role;   // "admin"
```

---

### 1.5 HTML Tables

```html
<table>
  <caption>Monthly Sales</caption>
  <thead>
    <tr>
      <th scope="col">Month</th>
      <th scope="col">Sales</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>January</td>
      <td>₹50,000</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <td colspan="2">Total: ₹50,000</td>
    </tr>
  </tfoot>
</table>
```

**`colspan`** — Merges across columns  
**`rowspan`** — Merges across rows  
**`scope`** — Helps screen readers associate headers with data cells  
**`<caption>`** — Provides a title for the table (accessibility)

---

### 1.6 HTML Head Elements

```html
<head>
  <!-- Character encoding -->
  <meta charset="UTF-8" />

  <!-- Viewport for responsive design -->
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />

  <!-- SEO -->
  <meta name="description" content="Page description for search engines" />
  <meta name="keywords" content="HTML, CSS, JavaScript" />
  <meta name="author" content="Kiran" />

  <!-- Open Graph (Social sharing) -->
  <meta property="og:title" content="Page Title" />
  <meta property="og:image" content="thumbnail.jpg" />

  <!-- Favicon -->
  <link rel="icon" href="favicon.ico" type="image/x-icon" />

  <!-- CSS -->
  <link rel="stylesheet" href="styles.css" />

  <!-- Preload important resources -->
  <link rel="preload" href="font.woff2" as="font" crossorigin />

  <!-- Page Title -->
  <title>My Website</title>
</head>
```

---

## 2. HTML FORMS

---

### 2.1 Form Structure

```html
<form action="/submit" method="POST" enctype="multipart/form-data">
  <!-- form elements -->
</form>
```

**`action`** — URL where form data is sent  
**`method`** — `GET` (data in URL) or `POST` (data in body)  
**`enctype`** — Required when uploading files: `multipart/form-data`

---

### 2.2 Input Types

```html
<!-- Text inputs -->
<input type="text" placeholder="Enter name" />
<input type="email" />
<input type="password" />
<input type="tel" />
<input type="url" />
<input type="search" />
<input type="number" min="1" max="100" step="1" />

<!-- Date/Time -->
<input type="date" />
<input type="time" />
<input type="datetime-local" />
<input type="month" />
<input type="week" />

<!-- Selection -->
<input type="checkbox" name="agree" value="yes" checked />
<input type="radio" name="gender" value="male" />
<input type="radio" name="gender" value="female" />

<!-- File & Color -->
<input type="file" accept=".jpg,.png" multiple />
<input type="color" value="#ff0000" />

<!-- Range & Hidden -->
<input type="range" min="0" max="100" value="50" />
<input type="hidden" name="token" value="abc123" />

<!-- Buttons -->
<input type="submit" value="Submit" />
<input type="reset" value="Reset" />
<input type="button" value="Click" />
```

---

### 2.3 Form Elements

```html
<!-- Textarea -->
<textarea name="message" rows="5" cols="30" placeholder="Your message..."></textarea>

<!-- Select dropdown -->
<select name="city">
  <option value="" disabled selected>Select city</option>
  <optgroup label="Maharashtra">
    <option value="mumbai">Mumbai</option>
    <option value="pune">Pune</option>
  </optgroup>
</select>

<!-- Datalist (autocomplete suggestions) -->
<input list="browsers" name="browser" />
<datalist id="browsers">
  <option value="Chrome" />
  <option value="Firefox" />
  <option value="Safari" />
</datalist>

<!-- Button -->
<button type="submit">Submit</button>
<button type="button" onclick="doSomething()">Click</button>
<button type="reset">Reset</button>

<!-- Fieldset and Legend (grouping) -->
<fieldset>
  <legend>Personal Info</legend>
  <label for="name">Name:</label>
  <input type="text" id="name" name="name" />
</fieldset>
```

---

### 2.4 Form Validation Attributes

```html
<input type="email" required />
<input type="text" minlength="3" maxlength="50" />
<input type="number" min="1" max="100" />
<input type="text" pattern="[A-Za-z]{3,}" title="At least 3 letters" />
<input type="url" />
<input type="text" autocomplete="name" />
<input type="text" readonly />
<input type="text" disabled />
<input type="text" autofocus />
```

**`required`** — Field must be filled before submit  
**`pattern`** — Regex pattern the value must match  
**`readonly`** — Value shown but cannot be changed  
**`disabled`** — Field inactive; value NOT submitted  
**`autocomplete`** — Browser fills in previously entered values

---

### 2.5 Label Best Practices

```html
<!-- Method 1: for + id (preferred) -->
<label for="email">Email:</label>
<input type="email" id="email" name="email" />

<!-- Method 2: wrapping -->
<label>
  Email:
  <input type="email" name="email" />
</label>
```

Always associate labels with inputs. Clicking the label focuses the input — critical for usability and accessibility.

---

## 3. HTML5 SEMANTIC ELEMENTS

---

### 3.1 Why Semantics Matter

Semantic HTML uses meaningful tags that describe the **purpose** of content, not just its appearance.

**Benefits:**
- Improved accessibility (screen readers understand structure)
- Better SEO (search engines understand content hierarchy)
- Easier maintenance and readability
- Consistent browser rendering

---

### 3.2 Semantic Layout Elements

```html
<header>
  <!-- Site logo, navigation, hero area -->
  <nav>
    <ul>
      <li><a href="/">Home</a></li>
      <li><a href="/about">About</a></li>
    </ul>
  </nav>
</header>

<main>
  <!-- Primary content of the page — only ONE per page -->

  <article>
    <!-- Self-contained content: blog post, news article, forum post -->
    <header>
      <h1>Article Title</h1>
      <time datetime="2024-01-15">January 15, 2024</time>
    </header>
    <p>Article content...</p>

    <section>
      <!-- Thematic grouping within article -->
      <h2>Section Heading</h2>
      <p>Section content...</p>
    </section>
  </article>

  <aside>
    <!-- Tangentially related content: sidebar, ads, related links -->
    <h2>Related Articles</h2>
  </aside>
</main>

<footer>
  <!-- Copyright, links, contact info -->
  <address>
    Contact: <a href="mailto:hi@example.com">hi@example.com</a>
  </address>
</footer>
```

---

### 3.3 article vs section vs div

| Element | Use When |
|---|---|
| `<article>` | Content that makes sense standalone (blog post, product card, comment) |
| `<section>` | Thematic grouping of related content — should have a heading |
| `<div>` | No semantic meaning; purely for styling/layout grouping |

**Rule of thumb:** If you can syndicate it (share it separately), use `<article>`. If it's just a chapter of something larger, use `<section>`. If it's purely for layout, use `<div>`.

---

### 3.4 Other Semantic Elements

```html
<figure>
  <!-- Self-contained content like image + caption -->
  <img src="chart.png" alt="Sales chart" />
  <figcaption>Figure 1: Monthly sales data</figcaption>
</figure>

<details>
  <!-- Disclosure widget — expandable -->
  <summary>Click to expand</summary>
  <p>Hidden content here...</p>
</details>

<dialog>
  <!-- Modal dialog box -->
  <p>Are you sure?</p>
  <button>Yes</button>
</dialog>

<progress value="70" max="100">70%</progress>
<meter value="0.6" min="0" max="1">60%</meter>

<time datetime="2024-01-15T10:30">January 15, 10:30 AM</time>

<mark>Highlighted search term</mark>

<wbr /> <!-- Word break opportunity -->
```

---

## 4. HTML ACCESSIBILITY (a11y)

---

### 4.1 ARIA (Accessible Rich Internet Applications)

ARIA attributes supplement HTML semantics to improve accessibility for dynamic content and custom UI widgets.

**ARIA Roles:**
```html
<div role="button" tabindex="0">Custom Button</div>
<div role="alert">Error: Invalid input</div>
<nav role="navigation" aria-label="Main Navigation">...</nav>
<div role="dialog" aria-labelledby="dialog-title">...</div>
```

**ARIA States and Properties:**
```html
<!-- aria-label: provides accessible name when no visible text -->
<button aria-label="Close modal">✕</button>

<!-- aria-labelledby: references another element as the label -->
<h2 id="section-title">Login</h2>
<section aria-labelledby="section-title">...</section>

<!-- aria-describedby: references descriptive text -->
<input aria-describedby="hint" />
<p id="hint">Must be at least 8 characters</p>

<!-- aria-hidden: hides element from screen readers -->
<span aria-hidden="true">🎉</span>

<!-- aria-expanded: state of collapsible elements -->
<button aria-expanded="false" aria-controls="menu">Menu</button>
<ul id="menu" hidden>...</ul>

<!-- aria-live: announces dynamic content changes -->
<div aria-live="polite">Content updates here</div>
<div aria-live="assertive">Urgent message</div>

<!-- aria-required, aria-invalid -->
<input aria-required="true" aria-invalid="false" />

<!-- aria-checked for custom checkboxes -->
<div role="checkbox" aria-checked="true">Accept Terms</div>
```

---

### 4.2 Accessibility Best Practices

1. **Always use semantic HTML first** — `<button>` over `<div role="button">`
2. **Every image needs alt text** — descriptive for informative images, `alt=""` for decorative
3. **Keyboard navigation** — All interactive elements must be focusable and operable via keyboard
4. **Focus management** — Visible focus indicators (never `outline: none` without alternative)
5. **Color contrast** — Minimum 4.5:1 ratio for normal text (WCAG AA)
6. **Form labels** — Every input must have an associated label
7. **Heading hierarchy** — Don't skip heading levels (h1 → h2 → h3)
8. **Skip links** — Allow keyboard users to skip repetitive navigation

```html
<!-- Skip link (first element in body) -->
<a href="#main-content" class="skip-link">Skip to main content</a>

<!-- Decorative image -->
<img src="divider.png" alt="" role="presentation" />

<!-- Icon button with accessible name -->
<button>
  <svg aria-hidden="true">...</svg>
  <span class="sr-only">Delete item</span>
</button>
```

```css
/* Visually hidden but accessible to screen readers */
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
```

---

## 5. HTML MEDIA & EMBEDDING

---

### 5.1 Images

```html
<!-- Responsive images with srcset -->
<img
  src="image-800.jpg"
  srcset="image-400.jpg 400w, image-800.jpg 800w, image-1200.jpg 1200w"
  sizes="(max-width: 600px) 400px, (max-width: 1000px) 800px, 1200px"
  alt="Description"
  loading="lazy"
/>

<!-- Art direction with picture element -->
<picture>
  <source media="(min-width: 800px)" srcset="desktop.jpg" />
  <source media="(min-width: 400px)" srcset="tablet.jpg" />
  <img src="mobile.jpg" alt="Responsive image" />
</picture>

<!-- Modern image formats -->
<picture>
  <source type="image/webp" srcset="image.webp" />
  <source type="image/avif" srcset="image.avif" />
  <img src="image.jpg" alt="Fallback" />
</picture>
```

---

### 5.2 Video and Audio

```html
<!-- Video -->
<video
  src="movie.mp4"
  width="640"
  height="360"
  controls
  autoplay
  muted
  loop
  poster="thumbnail.jpg"
  preload="metadata"
>
  <source src="movie.mp4" type="video/mp4" />
  <source src="movie.webm" type="video/webm" />
  <track kind="subtitles" src="captions.vtt" srclang="en" label="English" />
  Your browser does not support video.
</video>

<!-- Audio -->
<audio controls>
  <source src="audio.mp3" type="audio/mpeg" />
  <source src="audio.ogg" type="audio/ogg" />
  Your browser does not support audio.
</audio>
```

**`preload`** values: `none`, `metadata` (default), `auto`  
**`<track>`** — Provides captions, subtitles, descriptions for accessibility

---

### 5.3 Iframe and Embedding

```html
<!-- Embedding external content -->
<iframe
  src="https://www.youtube.com/embed/videoId"
  width="560"
  height="315"
  title="Video description"
  allow="accelerometer; autoplay; clipboard-write; encrypted-media"
  allowfullscreen
  loading="lazy"
  sandbox="allow-scripts allow-same-origin"
></iframe>
```

**`sandbox`** — Restricts capabilities of embedded content for security  
**`title`** — Required for accessibility (screen readers announce it)

---

## 6. HTML APIs & ADVANCED FEATURES

---

### 6.1 HTML5 APIs Overview

| API | Purpose |
|---|---|
| Canvas API | 2D/3D drawing and graphics |
| Web Storage | localStorage / sessionStorage |
| Geolocation | Get user's location |
| Drag and Drop | Native drag-and-drop |
| Web Workers | Background threads |
| WebSockets | Real-time communication |
| Service Workers | Offline caching, PWA |
| Fetch API | Modern HTTP requests |
| Intersection Observer | Detect when elements enter viewport |
| History API | Manipulate browser history |

### 6.2 Canvas

```html
<canvas id="myCanvas" width="400" height="200"></canvas>
```
```js
const canvas = document.getElementById("myCanvas");
const ctx = canvas.getContext("2d");
ctx.fillStyle = "blue";
ctx.fillRect(10, 10, 100, 80);
ctx.strokeStyle = "red";
ctx.beginPath();
ctx.arc(200, 100, 50, 0, Math.PI * 2);
ctx.stroke();
```

### 6.3 Template and Slot (Web Components)

```html
<template id="card-template">
  <div class="card">
    <slot name="title">Default Title</slot>
    <slot name="content"></slot>
  </div>
</template>
```
```js
const template = document.getElementById("card-template");
const clone = template.content.cloneNode(true);
document.body.appendChild(clone);
```

---

# CSS

---

## 7. CSS BASICS

---

### 7.1 What is CSS?

CSS (Cascading Style Sheets) controls the visual presentation of HTML. "Cascading" means styles apply in a specific priority order.

**Three ways to apply CSS:**

```html
<!-- 1. External stylesheet (BEST PRACTICE) -->
<link rel="stylesheet" href="styles.css" />

<!-- 2. Internal styles -->
<style>
  h1 { color: red; }
</style>

<!-- 3. Inline styles (AVOID unless dynamic/overriding) -->
<h1 style="color: red;">Heading</h1>
```

---

### 7.2 CSS Selectors

**Basic Selectors:**
```css
/* Universal */
* { box-sizing: border-box; }

/* Element / Type */
p { color: #333; }

/* Class */
.btn { padding: 8px 16px; }

/* ID (unique per page) */
#header { background: #fff; }

/* Attribute */
input[type="email"] { border: 1px solid blue; }
a[href^="https"] { color: green; }   /* starts with */
a[href$=".pdf"] { color: red; }      /* ends with */
a[href*="example"] { color: blue; }  /* contains */
```

**Combinator Selectors:**
```css
/* Descendant (any level) */
div p { color: gray; }

/* Child (direct child only) */
ul > li { list-style: none; }

/* Adjacent sibling (immediately after) */
h2 + p { margin-top: 0; }

/* General sibling (all siblings after) */
h2 ~ p { color: gray; }
```

**Pseudo-classes:**
```css
a:hover { color: blue; }
a:focus { outline: 2px solid blue; }
a:visited { color: purple; }
a:active { color: red; }

input:focus { border-color: blue; }
input:disabled { opacity: 0.5; }
input:checked { accent-color: green; }
input:valid { border-color: green; }
input:invalid { border-color: red; }

li:first-child { font-weight: bold; }
li:last-child { border-bottom: none; }
li:nth-child(2) { color: red; }
li:nth-child(odd) { background: #f5f5f5; }
li:nth-child(3n+1) { color: blue; }
li:not(.active) { opacity: 0.7; }
p:empty { display: none; }
```

**Pseudo-elements:**
```css
p::first-line { font-weight: bold; }
p::first-letter { font-size: 2em; }

/* Generated content */
.btn::before { content: "→ "; }
.required::after { content: " *"; color: red; }

/* Text selection */
::selection { background: yellow; color: black; }

/* Placeholder text */
input::placeholder { color: #aaa; }
```

---

### 7.3 CSS Units

**Absolute Units:**
| Unit | Description |
|---|---|
| `px` | Pixels — most common, device-independent |
| `pt` | Points (1pt = 1.33px) — for print |
| `cm`, `mm` | Centimeters, millimeters — for print |

**Relative Units:**
| Unit | Relative To |
|---|---|
| `em` | Parent element's font-size |
| `rem` | Root element (`<html>`) font-size |
| `%` | Parent element's corresponding property |
| `vw` | 1% of viewport width |
| `vh` | 1% of viewport height |
| `vmin` | 1% of smaller viewport dimension |
| `vmax` | 1% of larger viewport dimension |
| `ch` | Width of the "0" character in current font |

```css
html { font-size: 16px; }

/* rem — predictable, not affected by nesting */
h1 { font-size: 2rem; }   /* 32px */
p  { font-size: 1rem; }   /* 16px */

/* em — compounds with nesting */
.parent { font-size: 20px; }
.parent .child { font-size: 1.5em; }   /* 30px */
.parent .child .grandchild { font-size: 1.5em; }  /* 45px! */

/* Viewport units */
.hero { height: 100vh; width: 100vw; }
```

**Interview Tip:** Prefer `rem` for font sizes and `px` for borders/shadows. Use `em` for padding/margin that should scale with font size.

---

## 8. CSS BOX MODEL

---

### 8.1 The Box Model

Every HTML element is a rectangular box consisting of:

```
┌──────────────────────────────────┐
│             MARGIN               │
│  ┌────────────────────────────┐  │
│  │          BORDER            │  │
│  │  ┌──────────────────────┐  │  │
│  │  │       PADDING        │  │  │
│  │  │  ┌────────────────┐  │  │  │
│  │  │  │    CONTENT     │  │  │  │
│  │  │  └────────────────┘  │  │  │
│  │  └──────────────────────┘  │  │
│  └────────────────────────────┘  │
└──────────────────────────────────┘
```

- **Content** — The actual text/image
- **Padding** — Space between content and border (inside the element)
- **Border** — A line around the padding
- **Margin** — Space outside the border (between elements)

---

### 8.2 box-sizing

```css
/* Default — width/height applies to content only */
/* Total width = width + padding + border */
.default {
  box-sizing: content-box;
  width: 200px;
  padding: 20px;
  border: 5px solid black;
  /* Actual rendered width: 200 + 40 + 10 = 250px */
}

/* border-box — width/height includes padding and border */
/* Total width = exactly what you set */
.better {
  box-sizing: border-box;
  width: 200px;
  padding: 20px;
  border: 5px solid black;
  /* Actual rendered width: 200px */
}

/* Best practice: apply globally */
*, *::before, *::after {
  box-sizing: border-box;
}
```

---

### 8.3 Margin, Padding, Border

```css
/* Shorthand — Top Right Bottom Left (clockwise) */
margin: 10px 20px 10px 20px;
margin: 10px 20px;         /* top/bottom  left/right */
margin: 10px;              /* all sides */
margin: 10px 20px 15px;    /* top  left/right  bottom */

/* Individual */
margin-top: 10px;
margin-right: 20px;
margin-bottom: 10px;
margin-left: 20px;

/* Auto centering */
.container {
  width: 800px;
  margin: 0 auto;
}

/* Margin Collapse! */
/* Vertical margins between block elements collapse to the LARGER value */
.box1 { margin-bottom: 30px; }
.box2 { margin-top: 20px; }
/* Actual gap = 30px, not 50px */

/* Border shorthand */
border: 2px solid #333;
border: width style color;

/* Border styles: solid, dashed, dotted, double, groove, ridge, inset, outset, none */

/* Border radius */
border-radius: 8px;
border-radius: 50%;             /* circle */
border-radius: 10px 20px;       /* top-left/bottom-right  top-right/bottom-left */
border-radius: 10px 20px 30px 40px; /* TL TR BR BL */
```

---

### 8.4 Display Property

```css
display: block;          /* Full width, starts on new line */
display: inline;         /* Content width, no top/bottom margin */
display: inline-block;   /* Inline but respects width/height */
display: none;           /* Removes from layout (not visible, not in flow) */
display: flex;           /* Flexbox container */
display: inline-flex;    /* Inline flexbox */
display: grid;           /* Grid container */
display: inline-grid;    /* Inline grid */
display: table;          /* Table-like layout */
display: contents;       /* Element itself not rendered, children are */
```

**`display: none` vs `visibility: hidden`:**
- `display: none` — Element removed from layout (takes no space)
- `visibility: hidden` — Element invisible but still takes space

---

## 9. CSS LAYOUTS

---

### 9.1 Normal Flow

By default, block elements stack vertically and inline elements flow horizontally. This is the "normal flow."

### 9.2 Float (Legacy)

```css
img { float: left; margin-right: 20px; }

/* Clearfix — fixes container collapsing when children are floated */
.clearfix::after {
  content: "";
  display: table;
  clear: both;
}
```

Float was widely used for layouts before Flexbox/Grid. Now mainly used for text wrapping around images.

---

## 10. CSS FLEXBOX

---

### 10.1 Flexbox Concepts

Flexbox is a one-dimensional layout model (row OR column).

```css
.container {
  display: flex;
}
```

**Two axes:**
- **Main axis** — Direction of flex items (set by `flex-direction`)
- **Cross axis** — Perpendicular to main axis

---

### 10.2 Container Properties

```css
.container {
  display: flex;

  /* Direction */
  flex-direction: row;           /* default — left to right */
  flex-direction: row-reverse;   /* right to left */
  flex-direction: column;        /* top to bottom */
  flex-direction: column-reverse;/* bottom to top */

  /* Wrapping */
  flex-wrap: nowrap;    /* default — single line */
  flex-wrap: wrap;      /* wrap to next line */
  flex-wrap: wrap-reverse;

  /* Shorthand */
  flex-flow: row wrap;

  /* Main axis alignment */
  justify-content: flex-start;    /* default */
  justify-content: flex-end;
  justify-content: center;
  justify-content: space-between; /* gaps between items */
  justify-content: space-around;  /* gaps around items */
  justify-content: space-evenly;  /* equal gaps everywhere */

  /* Cross axis alignment (single line) */
  align-items: stretch;     /* default — fill cross axis */
  align-items: flex-start;
  align-items: flex-end;
  align-items: center;
  align-items: baseline;

  /* Cross axis alignment (multi-line) */
  align-content: flex-start;
  align-content: center;
  align-content: space-between;

  /* Gap between items */
  gap: 16px;
  gap: 16px 24px; /* row-gap  column-gap */
  row-gap: 16px;
  column-gap: 24px;
}
```

---

### 10.3 Flex Item Properties

```css
.item {
  /* Growth factor — how much to grow relative to other items */
  flex-grow: 0;   /* default — don't grow */
  flex-grow: 1;   /* grow to fill space equally */

  /* Shrink factor — how much to shrink if not enough space */
  flex-shrink: 1; /* default — can shrink */
  flex-shrink: 0; /* don't shrink */

  /* Base size before grow/shrink */
  flex-basis: auto;   /* default — use item's content size */
  flex-basis: 200px;
  flex-basis: 0;      /* start from 0, then grow */

  /* Shorthand: grow shrink basis */
  flex: 1;           /* flex: 1 1 0 */
  flex: 1 1 auto;
  flex: none;        /* flex: 0 0 auto — no grow, no shrink */
  flex: 0 0 200px;   /* fixed 200px */

  /* Individual alignment (overrides align-items) */
  align-self: auto;
  align-self: flex-start;
  align-self: center;
  align-self: stretch;

  /* Order (default: 0; lower = earlier) */
  order: 0;
  order: -1; /* moves to front */
  order: 2;  /* moves toward back */
}
```

**Common Flexbox Patterns:**
```css
/* Center anything */
.center {
  display: flex;
  justify-content: center;
  align-items: center;
}

/* Navigation bar */
.nav {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

/* Equal-width columns */
.col {
  flex: 1;
}

/* Push last item to end */
.container { display: flex; }
.spacer { flex: 1; }  /* pushes next item to right */
```

---

## 11. CSS GRID

---

### 11.1 Grid Concepts

Grid is a two-dimensional layout system (rows AND columns simultaneously).

```css
.container {
  display: grid;
}
```

---

### 11.2 Defining the Grid

```css
.container {
  display: grid;

  /* Define columns */
  grid-template-columns: 200px 200px 200px;
  grid-template-columns: 1fr 2fr 1fr;        /* fractional units */
  grid-template-columns: repeat(3, 1fr);     /* 3 equal columns */
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr)); /* responsive */
  grid-template-columns: 200px auto 100px;

  /* Define rows */
  grid-template-rows: 100px auto 100px;
  grid-template-rows: repeat(3, 1fr);

  /* Gap between cells */
  gap: 16px;
  row-gap: 16px;
  column-gap: 24px;

  /* Implicit rows (auto-generated) */
  grid-auto-rows: 100px;
  grid-auto-rows: minmax(100px, auto);

  /* Direction of auto-placement */
  grid-auto-flow: row;     /* default */
  grid-auto-flow: column;
  grid-auto-flow: dense;   /* fill gaps */
}
```

---

### 11.3 Grid Placement

```css
/* Line-based placement */
.item {
  grid-column: 1 / 3;        /* start at line 1, end at line 3 (span 2) */
  grid-column: 1 / span 2;   /* start at 1, span 2 columns */
  grid-column: 2 / -1;       /* from line 2 to last line */

  grid-row: 1 / 3;
  grid-row: span 2;          /* span 2 rows from wherever auto-placed */

  /* Shorthand: row-start / column-start / row-end / column-end */
  grid-area: 1 / 1 / 3 / 3;
}
```

**Named Template Areas:**
```css
.container {
  display: grid;
  grid-template-columns: 200px 1fr;
  grid-template-rows: auto 1fr auto;
  grid-template-areas:
    "header  header"
    "sidebar content"
    "footer  footer";
}

.header  { grid-area: header; }
.sidebar { grid-area: sidebar; }
.content { grid-area: content; }
.footer  { grid-area: footer; }
```

---

### 11.4 Grid Item Alignment

```css
/* Align all items within their cells */
.container {
  justify-items: start | end | center | stretch; /* horizontal */
  align-items: start | end | center | stretch;   /* vertical */
  place-items: center;                           /* shorthand */
}

/* Align the entire grid within the container */
.container {
  justify-content: start | end | center | space-between | space-around;
  align-content: start | end | center | space-between;
  place-content: center;
}

/* Override for individual item */
.item {
  justify-self: start | end | center | stretch;
  align-self: start | end | center | stretch;
  place-self: center;
}
```

---

### 11.5 Flexbox vs Grid — When to Use

| Situation | Use |
|---|---|
| One-dimensional layout (row or column) | Flexbox |
| Two-dimensional layout (rows AND columns) | Grid |
| Content-driven sizing (let content determine size) | Flexbox |
| Layout-driven sizing (define tracks first) | Grid |
| Navigation bar | Flexbox |
| Page layout (header/sidebar/footer) | Grid |
| Card components | Either |
| Complex overlapping elements | Grid |

---

## 12. CSS POSITIONING

---

### 12.1 Position Values

```css
/* Static (default) — normal document flow */
position: static;

/* Relative — offset from its normal position; still in flow */
position: relative;
top: 10px;   /* moves DOWN 10px from normal position */
left: 20px;  /* moves RIGHT 20px */

/* Absolute — removed from flow; positioned relative to nearest positioned ancestor */
position: absolute;
top: 0;
right: 0;

/* Fixed — removed from flow; positioned relative to viewport; doesn't scroll */
position: fixed;
bottom: 20px;
right: 20px;

/* Sticky — hybrid: relative until it hits a scroll threshold, then fixed */
position: sticky;
top: 0; /* sticks when it reaches top of viewport while scrolling */
```

**The "positioned ancestor" rule:** An absolutely positioned element looks for the nearest ancestor with `position` other than `static`. If none found, it positions relative to `<html>`.

```css
/* Common pattern: parent relative, child absolute */
.parent {
  position: relative;
}
.child {
  position: absolute;
  top: 0;
  right: 0;
}
```

---

### 12.2 z-index

Controls stacking order of positioned elements (only works on positioned elements).

```css
.modal-overlay { position: fixed; z-index: 1000; }
.modal         { position: fixed; z-index: 1001; }
.dropdown      { position: absolute; z-index: 100; }
.tooltip       { position: absolute; z-index: 200; }
```

**Stacking context:** A new stacking context is created by:
- `position: relative/absolute/fixed/sticky` + `z-index` other than auto
- `opacity` less than 1
- `transform`, `filter`, `will-change`
- `isolation: isolate`

---

## 13. CSS TYPOGRAPHY

---

### 13.1 Font Properties

```css
/* Font family — always include fallbacks */
font-family: "Inter", "Helvetica Neue", Arial, sans-serif;
font-family: "Georgia", "Times New Roman", serif;
font-family: "Courier New", Courier, monospace;

/* Font size */
font-size: 16px;
font-size: 1rem;
font-size: 1.25em;
font-size: clamp(1rem, 2.5vw, 2rem); /* responsive fluid size */

/* Font weight */
font-weight: 400;  /* normal */
font-weight: 700;  /* bold */
font-weight: 100 to 900 (multiples of 100);

/* Font style */
font-style: normal | italic | oblique;

/* Font variant */
font-variant: normal | small-caps;

/* Shorthand: style variant weight size/line-height family */
font: italic small-caps 700 1.2rem/1.6 "Inter", sans-serif;
```

---

### 13.2 Text Properties

```css
/* Alignment */
text-align: left | right | center | justify;

/* Decoration */
text-decoration: underline;
text-decoration: none;
text-decoration: line-through;
text-decoration: underline dotted red;

/* Transform */
text-transform: uppercase | lowercase | capitalize | none;

/* Spacing */
letter-spacing: 0.05em;  /* tracking */
word-spacing: 0.2em;
line-height: 1.6;        /* unitless preferred — relative to font-size */
line-height: 1.5rem;

/* Indent */
text-indent: 2em;

/* Overflow */
white-space: nowrap;
overflow: hidden;
text-overflow: ellipsis;  /* requires nowrap + overflow hidden */

/* Multi-line truncation */
display: -webkit-box;
-webkit-line-clamp: 3;
-webkit-box-orient: vertical;
overflow: hidden;

/* Word break */
word-break: break-all;
word-break: break-word;
overflow-wrap: anywhere;
```

---

### 13.3 Web Fonts

```css
/* Google Fonts (via link) */
/* <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap" rel="stylesheet"> */

/* Custom font with @font-face */
@font-face {
  font-family: "MyFont";
  src: url("myfont.woff2") format("woff2"),
       url("myfont.woff") format("woff");
  font-weight: 400;
  font-style: normal;
  font-display: swap; /* show fallback font until custom font loads */
}
```

**`font-display` values:**
- `auto` — browser default
- `block` — invisible text until font loads (FOIT)
- `swap` — show fallback, swap when loaded (FOUT)
- `fallback` — brief invisible period, then fallback; swap only if font loads fast
- `optional` — brief invisible, use only if already cached

---

## 14. CSS COLORS & BACKGROUNDS

---

### 14.1 Color Formats

```css
/* Named */
color: red;
color: transparent;

/* Hex */
color: #ff0000;
color: #f00;         /* shorthand */
color: #ff000080;    /* with alpha (last 2 digits) */

/* RGB / RGBA */
color: rgb(255, 0, 0);
color: rgba(255, 0, 0, 0.5);

/* HSL / HSLA */
/* Hue (0-360°), Saturation (%), Lightness (%) */
color: hsl(0, 100%, 50%);         /* pure red */
color: hsla(0, 100%, 50%, 0.5);

/* Modern syntax (CSS Color Level 4) */
color: rgb(255 0 0 / 50%);
color: hsl(0 100% 50% / 0.5);

/* oklch (perceptually uniform, wide gamut) */
color: oklch(0.6 0.2 30);
```

---

### 14.2 Background Properties

```css
.box {
  background-color: #f5f5f5;

  /* Image */
  background-image: url("image.jpg");
  background-image: linear-gradient(to right, #ff0000, #0000ff);
  background-image: radial-gradient(circle, #ff0000, #0000ff);
  background-image: conic-gradient(from 0deg, red, blue, green, red);

  /* Multiple backgrounds (comma-separated, first = on top) */
  background-image: url("overlay.png"), url("bg.jpg");

  /* Size */
  background-size: auto;         /* default */
  background-size: cover;        /* fill container, may crop */
  background-size: contain;      /* fit inside container, may show gaps */
  background-size: 100% 100%;
  background-size: 200px 100px;

  /* Position */
  background-position: center;
  background-position: top right;
  background-position: 50% 50%;
  background-position: 20px 40px;

  /* Repeat */
  background-repeat: no-repeat;
  background-repeat: repeat-x;
  background-repeat: repeat-y;

  /* Attachment */
  background-attachment: scroll;  /* default */
  background-attachment: fixed;   /* parallax effect */
  background-attachment: local;

  /* Origin and clip */
  background-origin: border-box | padding-box | content-box;
  background-clip: border-box | padding-box | content-box | text;

  /* Shorthand: color image position/size repeat attachment origin clip */
  background: #f5f5f5 url("bg.jpg") center/cover no-repeat;
}
```

---

## 15. CSS TRANSITIONS & ANIMATIONS

---

### 15.1 Transitions

Transitions smoothly animate changes between CSS property values.

```css
.btn {
  background-color: blue;
  color: white;
  transform: scale(1);

  /* property duration timing-function delay */
  transition: background-color 0.3s ease, transform 0.2s ease-in-out;

  /* Or transition all properties */
  transition: all 0.3s ease;
}

.btn:hover {
  background-color: darkblue;
  transform: scale(1.05);
}
```

**Timing Functions:**
```css
transition-timing-function: linear;
transition-timing-function: ease;        /* default */
transition-timing-function: ease-in;
transition-timing-function: ease-out;
transition-timing-function: ease-in-out;
transition-timing-function: cubic-bezier(0.25, 0.1, 0.25, 1.0);
transition-timing-function: steps(4, end); /* stepped transitions */
```

**Performant properties to transition:** `transform`, `opacity` (GPU-accelerated; don't trigger layout recalculation)

**Avoid transitioning:** `width`, `height`, `top`, `left`, `margin`, `padding` (trigger layout reflow)

---

### 15.2 Animations

Animations allow multi-step transitions using `@keyframes`.

```css
/* Define the animation */
@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes pulse {
  0%   { transform: scale(1); }
  50%  { transform: scale(1.1); }
  100% { transform: scale(1); }
}

/* Apply the animation */
.card {
  animation-name: fadeInUp;
  animation-duration: 0.5s;
  animation-timing-function: ease-out;
  animation-delay: 0.2s;
  animation-iteration-count: 1;       /* or infinite */
  animation-direction: normal;        /* alternate | reverse */
  animation-fill-mode: both;          /* forwards | backwards | both */
  animation-play-state: running;      /* or paused */

  /* Shorthand: name duration timing-function delay iteration direction fill-mode */
  animation: fadeInUp 0.5s ease-out 0.2s both;
}

/* Pause on hover */
.card:hover {
  animation-play-state: paused;
}
```

**`animation-fill-mode`:**
- `none` — Default; no styles applied before/after
- `forwards` — Retains final keyframe styles after animation ends
- `backwards` — Applies first keyframe styles before delay starts
- `both` — Combination of forwards and backwards

---

### 15.3 Transforms

```css
/* 2D Transforms */
transform: translate(50px, 100px);
transform: translateX(50px);
transform: translateY(-20px);
transform: scale(1.5);
transform: scale(2, 0.5);           /* scaleX, scaleY */
transform: rotate(45deg);
transform: skew(15deg, 10deg);

/* Multiple transforms (apply right to left) */
transform: translate(-50%, -50%) rotate(45deg) scale(1.2);

/* 3D Transforms */
transform: rotateX(45deg);
transform: rotateY(45deg);
transform: translateZ(100px);
transform: perspective(500px) rotateY(30deg);

/* Transform origin */
transform-origin: center;        /* default */
transform-origin: top left;
transform-origin: 50% 50%;
transform-origin: 0 0;
```

---

## 16. CSS RESPONSIVE DESIGN

---

### 16.1 Media Queries

```css
/* Mobile-first (recommended) */
/* Base styles are for mobile */
.container { width: 100%; padding: 16px; }

/* Tablet */
@media (min-width: 768px) {
  .container { max-width: 768px; padding: 24px; }
}

/* Desktop */
@media (min-width: 1024px) {
  .container { max-width: 1200px; padding: 32px; }
}

/* Desktop-first */
@media (max-width: 768px) {
  /* tablet and below */
}

/* Other media features */
@media (orientation: landscape) { }
@media (prefers-color-scheme: dark) {
  body { background: #1a1a1a; color: #fff; }
}
@media (prefers-reduced-motion: reduce) {
  * { animation: none !important; transition: none !important; }
}
@media print {
  .no-print { display: none; }
}

/* Combined conditions */
@media (min-width: 768px) and (max-width: 1024px) { }
@media (min-width: 768px), (orientation: landscape) { } /* OR */
@media not (max-width: 600px) { }

/* Container queries (modern) */
.parent { container-type: inline-size; }
@container (min-width: 400px) {
  .child { font-size: 1.5rem; }
}
```

**Common Breakpoints:**
```
320px  — Small phones
480px  — Large phones
768px  — Tablets
1024px — Small laptops
1280px — Desktops
1536px — Large desktops
```

---

### 16.2 Responsive Units and Techniques

```css
/* Fluid typography */
font-size: clamp(1rem, 2.5vw, 2rem);
/* min  |  preferred  |  max */

/* Fluid spacing */
padding: clamp(16px, 4vw, 48px);

/* Responsive images */
img {
  max-width: 100%;
  height: auto;
}

/* Responsive grid (no media queries) */
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 24px;
}

/* Aspect ratio */
.video-wrapper {
  aspect-ratio: 16 / 9;
  width: 100%;
}
```

---

## 17. CSS VARIABLES (CUSTOM PROPERTIES)

---

### 17.1 Defining and Using Variables

```css
/* Define in :root for global scope */
:root {
  --color-primary: #3b82f6;
  --color-secondary: #10b981;
  --color-text: #1f2937;
  --color-bg: #ffffff;

  --spacing-sm: 8px;
  --spacing-md: 16px;
  --spacing-lg: 24px;
  --spacing-xl: 32px;

  --font-size-base: 1rem;
  --font-size-lg: 1.25rem;
  --font-size-xl: 1.5rem;

  --border-radius: 8px;
  --shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
  --transition: 0.3s ease;
}

/* Use with var() */
.btn {
  background-color: var(--color-primary);
  padding: var(--spacing-sm) var(--spacing-md);
  border-radius: var(--border-radius);
  transition: background-color var(--transition);
}

/* Fallback value */
.box {
  color: var(--color-text, #333);
}
```

---

### 17.2 Dynamic Theming with Variables

```css
/* Light theme (default) */
:root {
  --bg: #ffffff;
  --text: #1f2937;
  --card-bg: #f9fafb;
}

/* Dark theme */
[data-theme="dark"] {
  --bg: #1a1a2e;
  --text: #e2e8f0;
  --card-bg: #16213e;
}

/* Or using prefers-color-scheme */
@media (prefers-color-scheme: dark) {
  :root {
    --bg: #1a1a2e;
    --text: #e2e8f0;
  }
}

body {
  background-color: var(--bg);
  color: var(--text);
}
```

```js
// Switching themes with JavaScript
document.documentElement.setAttribute("data-theme", "dark");
// or
document.documentElement.style.setProperty("--color-primary", "#ff0000");
```

---

### 17.3 CSS Variables vs SASS Variables

| Feature | CSS Variables | SASS Variables |
|---|---|---|
| Runtime access | Yes (JS can read/write) | No (compiled away) |
| Cascades and inherits | Yes | No |
| Responsive (media query) | Yes | No |
| Browser support | Modern browsers | All (compiled) |
| Dynamic updates | Yes | No |

---

## 18. CSS SPECIFICITY & CASCADE

---

### 18.1 The Cascade

When multiple rules target the same element, CSS uses this priority order:

1. **Origin** — Browser > User > Author (your CSS)
2. **Specificity** — More specific selector wins
3. **Order** — Later declaration wins (same specificity)

---

### 18.2 Specificity Calculation

Specificity is calculated as `(a, b, c)`:

| Selector | a (IDs) | b (Classes/Attrs/Pseudo-classes) | c (Elements/Pseudo-elements) |
|---|---|---|---|
| `*` | 0 | 0 | 0 |
| `p` | 0 | 0 | 1 |
| `.class` | 0 | 1 | 0 |
| `#id` | 1 | 0 | 0 |
| `p.class` | 0 | 1 | 1 |
| `#id .class p` | 1 | 1 | 1 |
| `style=""` (inline) | Highest (overrides everything except !important) |
| `!important` | Overrides all |

```css
p { color: blue; }               /* (0,0,1) */
.text { color: green; }          /* (0,1,0) */
p.text { color: red; }           /* (0,1,1) */
#main p.text { color: purple; }  /* (1,1,1) */

/* !important — avoid in production code */
p { color: pink !important; }    /* wins over everything */
```

**Best Practice:** Keep specificity low. Use classes primarily, avoid IDs for styling, never use `!important` unless absolutely necessary.

---

### 18.3 Inheritance

Some CSS properties are inherited by child elements:

**Inherited by default:** `color`, `font-*`, `text-*`, `line-height`, `visibility`, `cursor`, `list-style-*`

**NOT inherited:** `margin`, `padding`, `border`, `background`, `width`, `height`, `display`, `position`

```css
/* Explicitly control inheritance */
.child {
  color: inherit;      /* force inherit */
  color: initial;      /* reset to browser default */
  color: unset;        /* inherit if inheritable, else initial */
  color: revert;       /* revert to browser stylesheet */
}

/* all shorthand */
.reset {
  all: unset;     /* reset ALL properties */
}
```

---

## 19. CSS PREPROCESSORS

---

### 19.1 SASS / SCSS

SASS adds programming features to CSS. SCSS syntax is CSS-compatible.

```scss
// Variables
$primary: #3b82f6;
$spacing: 16px;

// Nesting
.nav {
  display: flex;

  &__item {            // .nav__item (BEM)
    padding: $spacing;

    &:hover {          // .nav__item:hover
      color: $primary;
    }

    &.active {         // .nav__item.active
      font-weight: bold;
    }
  }
}

// Mixin — reusable CSS blocks
@mixin flex-center {
  display: flex;
  justify-content: center;
  align-items: center;
}

@mixin respond-to($breakpoint) {
  @if $breakpoint == "tablet" {
    @media (min-width: 768px) { @content; }
  }
}

.card {
  @include flex-center;

  @include respond-to("tablet") {
    padding: 32px;
  }
}

// Extend / Placeholder
%button-base {
  padding: 8px 16px;
  border: none;
  cursor: pointer;
}

.btn-primary {
  @extend %button-base;
  background: $primary;
}

// Functions
@function rem($px, $base: 16) {
  @return ($px / $base) * 1rem;
}

h1 { font-size: rem(32); } // 2rem

// Loops
@for $i from 1 through 5 {
  .col-#{$i} { width: 20% * $i; }
}
```

---

## 20. CSS ADVANCED TOPICS

---

### 20.1 CSS Pseudo-elements for Effects

```css
/* Tooltip with pure CSS */
.tooltip {
  position: relative;
}
.tooltip::after {
  content: attr(data-tooltip);
  position: absolute;
  bottom: 100%;
  left: 50%;
  transform: translateX(-50%);
  background: rgba(0,0,0,0.8);
  color: white;
  padding: 4px 8px;
  border-radius: 4px;
  white-space: nowrap;
  opacity: 0;
  transition: opacity 0.2s;
}
.tooltip:hover::after {
  opacity: 1;
}
```

---

### 20.2 CSS Filter and Backdrop Filter

```css
/* Filter — applies to element and its contents */
img {
  filter: blur(4px);
  filter: brightness(1.5);
  filter: contrast(200%);
  filter: grayscale(100%);
  filter: hue-rotate(90deg);
  filter: invert(100%);
  filter: opacity(50%);
  filter: saturate(200%);
  filter: sepia(100%);
  filter: drop-shadow(4px 4px 8px rgba(0,0,0,0.5));

  /* Multiple filters */
  filter: grayscale(50%) blur(2px);
}

/* Backdrop filter — applies to area behind element */
.frosted-glass {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
}
```

---

### 20.3 CSS clip-path

```css
/* Clip to geometric shapes */
.circle  { clip-path: circle(50%); }
.ellipse { clip-path: ellipse(50% 30%); }
.triangle {
  clip-path: polygon(50% 0%, 0% 100%, 100% 100%);
}
.hexagon {
  clip-path: polygon(25% 0%, 75% 0%, 100% 50%, 75% 100%, 25% 100%, 0% 50%);
}
.inset {
  clip-path: inset(10% 20% 10% 20% round 10px);
}
```

---

### 20.4 CSS Logical Properties

Logical properties adapt to writing direction and text orientation.

```css
/* Physical → Logical equivalents */
margin-left: 16px;   → margin-inline-start: 16px;
margin-right: 16px;  → margin-inline-end: 16px;
margin-top: 16px;    → margin-block-start: 16px;
margin-bottom: 16px; → margin-block-end: 16px;

padding-left: 8px;   → padding-inline-start: 8px;

width: 100px;        → inline-size: 100px;
height: 100px;       → block-size: 100px;

border-top: 1px solid → border-block-start: 1px solid;
text-align: left;    → text-align: start;
```

---

### 20.5 CSS Scroll Behavior

```css
/* Smooth scrolling */
html {
  scroll-behavior: smooth;
}

/* Scroll snap */
.scroll-container {
  overflow-x: scroll;
  scroll-snap-type: x mandatory;
}

.slide {
  scroll-snap-align: start;
  flex: 0 0 100%;
}

/* Scroll padding (offset for fixed headers) */
html {
  scroll-padding-top: 80px; /* height of sticky header */
}

/* Overscroll behavior */
.modal-body {
  overscroll-behavior: contain; /* prevent scroll from propagating */
}
```

---

### 20.6 CSS Performance Best Practices

1. **Use `transform` and `opacity` for animations** — GPU-composited, no layout reflow
2. **Avoid `*` selector** — Apply `box-sizing` with inheritance pattern instead
3. **Use `will-change` sparingly** — Hints browser to promote to its own layer
4. **Minimize specificity** — Easier to override, less cascade issues
5. **Use CSS variables** — Better than repeating values
6. **Critical CSS** — Inline above-the-fold styles; lazy-load the rest
7. **Use `contain`** — Isolate layout/paint/size containment
8. **Prefer logical properties** — Better for internationalization

```css
/* will-change — only for elements that WILL animate */
.animated-card {
  will-change: transform, opacity;
}

/* CSS containment */
.card {
  contain: layout style; /* isolated from rest of document */
}
```

---

### 20.7 BEM Naming Convention

BEM (Block, Element, Modifier) is a CSS naming methodology.

```
Block     — Standalone component: .card
Element   — Part of block: .card__title, .card__image
Modifier  — Variant/state: .card--featured, .card__title--large
```

```html
<div class="card card--featured">
  <img class="card__image" src="..." alt="..." />
  <div class="card__body">
    <h2 class="card__title card__title--large">Title</h2>
    <p class="card__text">Text</p>
    <button class="card__btn card__btn--primary">Read More</button>
  </div>
</div>
```

```css
.card { border-radius: 8px; overflow: hidden; }
.card--featured { border: 2px solid gold; }
.card__title { font-size: 1.2rem; }
.card__title--large { font-size: 1.8rem; }
```

---

## 21. QUICK REFERENCE: COMMON INTERVIEW QUESTIONS

---

### HTML Questions

| Question | Key Answer |
|---|---|
| What is DOCTYPE? | Declares document type; `<!DOCTYPE html>` for HTML5 |
| `<b>` vs `<strong>` | `<b>` is visual bold; `<strong>` implies importance |
| `<i>` vs `<em>` | `<i>` is visual italic; `<em>` implies emphasis |
| `id` vs `class` | `id` is unique per page; `class` can be used multiple times |
| `<section>` vs `<div>` | `<section>` is semantic with meaning; `<div>` has no meaning |
| `<article>` vs `<section>` | `<article>` is standalone/shareable; `<section>` is a grouping |
| What is `alt` in `<img>`? | Accessible description; shown when image fails to load |
| GET vs POST | GET appends data to URL; POST sends in body |
| `defer` vs `async` | `defer`: after HTML parsed; `async`: as soon as downloaded |
| What is `data-*`? | Custom attributes to store data on HTML elements |

### CSS Questions

| Question | Key Answer |
|---|---|
| `content-box` vs `border-box` | content-box: size = content only; border-box: size includes padding+border |
| How to center with Flexbox | `display:flex; justify-content:center; align-items:center` |
| What is specificity? | Weight of selector: inline > ID > class > element |
| Flexbox vs Grid | Flexbox = 1D (row/column); Grid = 2D (rows AND columns) |
| What is the cascade? | Priority order: origin → specificity → order |
| `position: sticky` vs `fixed` | sticky: relative until scroll threshold; fixed: always fixed to viewport |
| CSS variables vs SASS variables | CSS vars: runtime, JS-accessible, inheritable; SASS: compile-time only |
| What causes margin collapse? | Adjacent vertical block margins collapse to the larger value |
| `em` vs `rem` | em: relative to parent; rem: relative to root (`<html>`) |
| What is a stacking context? | Isolated group of elements stacked together in z-axis |
| Performant CSS animations | Animate `transform` and `opacity` only (GPU-accelerated) |
| `display:none` vs `visibility:hidden` | `none`: removed from layout; `hidden`: invisible but takes space |

---

### Bonus: Script Loading Strategies

```html
<!-- Blocks HTML parsing until script downloaded + executed -->
<script src="app.js"></script>

<!-- Downloads in parallel; executes immediately when done (order not guaranteed) -->
<script src="app.js" async></script>

<!-- Downloads in parallel; executes AFTER HTML is fully parsed (order preserved) -->
<script src="app.js" defer></script>
```

**Best practice:** Use `defer` for regular scripts. Use `async` only for independent scripts (analytics, ads).

---

*Good luck with your HTML & CSS interviews!*
