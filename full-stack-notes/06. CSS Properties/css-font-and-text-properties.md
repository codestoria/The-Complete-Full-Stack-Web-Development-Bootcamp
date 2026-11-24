# CSS Font and Text Properties

## Contents

1. [Why Font Properties Matter](#1-why-font-properties-matter)
2. [`font-size`](#2-font-size)
3. [`font-weight`](#3-font-weight)
4. [`font-style`](#4-font-style)
5. [`font-family`](#5-font-family)
6. [`text-align`](#6-text-align)
7. [`line-height` (A Brief Note)](#7-line-height-a-brief-note)
8. [Putting It All Together](#8-putting-it-all-together)
9. [Quick Reference](#9-quick-reference)
10. [Key Takeaways](#10-key-takeaways)

---

## 1. Why Font Properties Matter

Text is the most common way information is communicated on a web page. Before you style a heading or adjust a paragraph, the browser must make several decisions about how to draw that text:

- How big should each character be?
- How thick should the strokes be?
- Should the letters lean (italic) or stand upright?
- Which typeface should be used, and what happens if that typeface is not available?
- Where should the text sit horizontally within its container?
- How much space should there be between lines?

CSS provides one property for each of these questions. Getting them right matters for two reasons:

1. **Readability.** Text that is too small, too tightly spaced, or set in a poorly chosen typeface is hard to read. Users will leave.
2. **Consistency across devices.** Your page will be viewed on phones, laptops, and large monitors, each with different screen sizes and default settings. The units and fallback mechanisms you choose determine whether your text looks right everywhere.

With that motivation in mind, let's look at each property in turn.

---

## 2. `font-size`

The `font-size` property sets the size of text.

```css
p {
  font-size: 20px;
}
```

You can specify the size in several units. Choosing the right unit matters because it determines how your text scales when the page is resized or when a user changes their browser's default font size.

### Absolute Units: `px` and `pt`

An **absolute** unit produces the same size regardless of surrounding context.

**Pixels (`px`)**

```css
p {
  font-size: 20px;
}
```

A pixel is a tiny unit of measurement. In CSS, `1px` is defined as **1/96 of an inch** — specifically, a **CSS reference pixel**, not a physical pixel on your screen. Because it is an absolute unit, `20px` is always `20px` no matter what element it appears in or what the parent's font size is.

> **Note on high-resolution screens:** On a "Retina" or high-DPI display, one CSS pixel may be rendered using multiple physical pixels. The CSS unit itself does not change — the browser uses more physical pixels to draw the same CSS size, which is why a `20px` font looks sharp on both a standard monitor and a high-DPI phone.

**Points (`pt`)**

```css
p {
  font-size: 12pt;
}
```

A point is slightly larger than a pixel: **1/72 of an inch**, or about **0.35 mm**. Points are common in print design and in applications like Microsoft Word — when you select a "12" font size in Word, you are selecting 12 points.

> **Note:** On the web, `px` and `rem` are far more common than `pt`. Points are mainly useful in print stylesheets, where the output is measured in physical units.

### Relative Units: `em` and `rem`

**Relative** units derive their size from something else. This makes them more flexible, but it also means you need to understand what they are relative to.

#### Understanding Inheritance First

To understand `em`, you first need to know about **inheritance**. In CSS, if an element does not set its own `font-size`, it inherits the font size of its parent. This inheritance chain continues up to the root element (`<html>`), which has a browser-defined default (usually `16px`).

This is why changing the `font-size` on `<body>` affects all the text inside it — unless a child element overrides that value.

#### `em`

The unit is written `em` and pronounced like the name of the letter **M** (/ɛm/), not as the two letters "E-M." The name is often described as coming from the width of the capital letter **M** in a typeface; in modern CSS, however, `1em` is simply relative to the relevant `font-size`, as explained below.

The key rule is this: **`em` is a multiplier applied to a font size.** Which font size depends on the property it is used on:

- **On the `font-size` property itself:** `1em` equals the **parent's font size**. So `font-size: 2em` means "twice the parent's font size."
- **On properties such as `padding`, `margin`, `width`, or `line-height`:** `1em` equals the **element's own computed `font-size`** (whether set directly or inherited).

```css
body {
  font-size: 20px;
}

h1 {
  font-size: 2em; /* 2 × 20px = 40px */
}
```

If the parent (`body`) has `font-size: 20px`, then `1em` inside it equals `20px`, and `2em` equals `40px`. If you later change the body to `40px`, the `h1` automatically becomes `80px`.

This is powerful, but it can become confusing when elements are nested several levels deep. Because each element's `em` is based on its own inherited size, nested elements **multiply**:

```css
body   { font-size: 20px; }
div    { font-size: 1.5em; }  /* 1.5 × 20px = 30px */
div p  { font-size: 1.5em; }  /* 1.5 × 30px = 45px, not 30px */
```

The paragraph ends up at `45px`, not `30px`, because its `1.5em` is relative to the `div`'s `30px`, not the body's `20px`. You have to trace the chain of parents to know what `1em` actually resolves to — and that chain can be hard to follow when your CSS lives in a separate file from your HTML.

#### `rem` (Root Em)

The `rem` unit is **relative to the root element** (usually the `<html>` element), not the immediate parent.

```css
html {
  font-size: 20px;
}

h2 {
  font-size: 2rem; /* 2 × 20px = 40px, regardless of parent */
}
```

Because `rem` always refers to the same root, it is **consistent and predictable**. If you change the root font size, every `rem`-based size scales together — which is excellent for responsive design and accessibility.

#### Comparing `em` and `rem`

Consider this HTML:

```html
<body>
  <footer>
    <h2>Subtitle</h2>
  </footer>
</body>
```

If `footer` has `font-size: 20px` and `h2` has `font-size: 2em`, the `h2` becomes `40px`. If you later change `footer` to `80px`, the `h2` becomes `160px` — its size moves with its parent.

If instead `h2` has `font-size: 2rem`, changing `footer` has **no effect**. Only changing the root (`html`) font size affects it.

> **Recommendation:** Use `rem` for font sizes. It gives you a single point of control (the root size) and avoids the confusion of tracing nested parent sizes — especially when your CSS lives in a separate file from your HTML.

### Named Sizes

CSS also provides keywords: `small`, `medium`, `large`, `x-large`, `xx-large`, etc.

```css
h1 {
  font-size: x-large;
}
```

These are convenient but less precise. Their exact pixel values are defined by the browser and can vary slightly between browsers, so they are best used for quick prototypes rather than precise designs.

### Summary of `font-size` Units

| Unit | Relative to | Example | Behavior |
|------|-------------|---------|----------|
| `px` | Nothing (absolute) | `20px` | Always 20px |
| `pt` | Nothing (absolute) | `12pt` | Always 12pt (= 16px) |
| `em` | On `font-size`: parent's font size. On other properties: the element's own `font-size`. | `2em` | Doubles the relevant base size; compounds when nested |
| `rem` | Root font size | `2rem` | Doubles the root's size; consistent regardless of nesting |
| keyword | Browser default | `large` | Predefined scale, varies slightly between browsers |

---

## 3. `font-weight`

The `font-weight` property controls how light or bold the text appears. It answers the question: "How thick should the strokes of each character be?"

**Keyword values:**

```css
p {
  font-weight: normal; /* default, equivalent to 400 */
}

strong {
  font-weight: bold;   /* equivalent to 700 */
}
```

**Numeric values (commonly 100–900; newer CSS allows 1–1000):**

```css
h1 {
  font-weight: 900; /* very heavy */
}

.light {
  font-weight: 300; /* light */
}
```

The commonly used scale runs from `100` (thinnest) to `900` (thickest). Newer CSS specifications allow the full range `1`–`1000`, though not every font or browser supports every value. If a weight is unavailable, the browser picks the closest available one. This is why a `font-weight: 900` rule might look identical to `font-weight: 700` on a font that only ships in regular and bold.

> **Key idea:** `font-weight` is a request, not a command. You ask for a specific weight; the browser provides the closest weight available in the font you are using.

**Relative keywords:**

- `lighter` — selects a lighter weight relative to the inherited weight.
- `bolder` — selects a heavier weight relative to the inherited weight.

The exact mapping depends on the inherited weight and on which weights the font actually provides. In practice, these keywords move you to a noticeably lighter or heavier available weight — not necessarily a single step on the 100–900 scale. The precise mapping is defined by the CSS Fonts specification (based on the inherited weight and the available weights); if precision matters, set the numeric value directly.

> **Note:** `font-weight` only works as expected if the font family you are using actually includes those weights. Many web fonts offer only regular (400) and bold (700). If you need a range of weights, check the font's available weights before designing around them.

---

## 4. `font-style`

The `font-style` property controls whether text is upright or slanted (italic).

```css
em {
  font-style: italic;
}

p {
  font-style: normal; /* default */
}
```

Common values:

- `normal` — upright text. This is the default.
- `italic` — uses the font's italic version if available; otherwise, the browser synthesizes a slanted version.
- `oblique` — selects the font's oblique face if available; otherwise, the browser may synthesize a slanted version. In practice, `oblique` and `italic` often look the same, because many fonts do not have a separate italic or oblique design. Modern CSS also allows an optional angle, e.g. `font-style: oblique 10deg;`.

> **Important:** `font-style` and `font-weight` are different properties. `font-style` controls slant (italic), while `font-weight` controls thickness. Do not confuse them.

---

## 5. `font-family`

The `font-family` property specifies the typeface used for text.

```css
body {
  font-family: Helvetica, Arial, sans-serif;
}
```

This rule says: "Use Helvetica if it is available; otherwise use Arial; otherwise fall back to the browser's generic sans-serif font."

### Why the Fallback List Matters

`Helvetica` is commonly available on macOS, but many Windows computers do not have it installed. If you wrote only `font-family: Helvetica;` and the user did not have Helvetica, the browser would fall back to its default font — which might be something quite different.

By providing a comma-separated list of alternatives ending in a **generic family**, you ensure a sensible result on every system. This is not a minor detail: fonts are installed on the user's operating system, not bundled with your website (unless you load a web font — see below). You cannot assume that any specific font is present on every device.

### Generic Font Families

| Generic family | Description | Example typefaces |
|----------------|-------------|-------------------|
| `serif` | Has small decorative "feet" at the edges of letters | Times New Roman, Georgia |
| `sans-serif` | Clean edges, no feet | Arial, Helvetica, Verdana |
| `monospace` | Every character occupies the same width | Courier New, Consolas |
| `cursive` | Curly, handwriting-like | Platform-dependent; often a script or handwriting font |
| `fantasy` | Decorative, playful | Platform-dependent; often a display or decorative font |

Generic families are resolved by the browser and operating system, so they look different on different machines — but they always produce *something* reasonable. This is why they are the correct final entry in every `font-family` list.

> **Do not quote generic families.** Write `serif`, `sans-serif`, `monospace`, not `"serif"` or `"sans-serif"`. Quoting turns the name into a specific font-family name lookup, so the fallback may silently fail.

### Font Names with Spaces

If a font name contains spaces, it is best to wrap it in quotation marks:

```css
p {
  font-family: "Times New Roman", Times, serif;
}
```

Quotation marks are not strictly required for every multi-word name — the CSS specification allows an unquoted family name to consist of several identifiers separated by spaces. But quoting is the safer habit: it removes any ambiguity, and it is required when the name contains characters that are not valid in an identifier (e.g., a name starting with a digit, or containing punctuation). It also makes the intent obvious to anyone reading the CSS.

### Using Custom Web Fonts

If you want a font that is not installed on your users' computers, you can load it from a web font service. **Google Fonts** (fonts.google.com) is a free and popular option.

The process:

1. Browse fonts.google.com and select a font (and optionally specific weights, e.g., regular 400, bold 700).
2. Copy the `<link>` tag that Google provides.
3. Paste the `<link>` into the `<head>` of your HTML, **before** your `<style>` tag or external stylesheet link.
4. Copy the `font-family` CSS rule that Google provides (or write your own) and apply it to your elements.

> **Placement note:** Put the Google Fonts `<link>` inside `<head>`, **outside** `<style>`. Placing it before your own `<style>` tag (or external stylesheet link) is a common convention and makes the reading order clear, but it is not a hard requirement — the `<link>` mainly registers `@font-face` rules. The key requirement is that the `<link>` is in `<head>` and not inside `<style>`.

Example:

```html
<head>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Caveat:wght@400;700&amp;display=swap">
  <style>
    h1 {
      font-family: "Caveat", cursive;
    }
  </style>
</head>
```

Because the font is loaded from Google's servers, it works for any user who can reach those servers, regardless of their operating system or installed fonts. If the request fails (offline, blocked, or the service is down), the browser falls back to the next font in the list — which is exactly why the generic fallback matters.

> **Important:** Always include a generic fallback (e.g., `cursive`) after your custom font name, in case the link fails to load. A missing font should never make your text unreadable.

---

## 6. `text-align`

The `text-align` property controls the horizontal alignment of text within its container.

```css
h1 {
  text-align: center;
}

p {
  text-align: left; /* explicit left; per CSS Text Level 3 the initial value is `start`, which resolves to left in LTR (older specs/implementations historically behaved as `left`) */
}

.right-aligned {
  text-align: right;
}
```

You can also use `start` and `end`, which are **direction-aware** (they depend on the element's writing direction):

- In a left-to-right language (English), `start` = left, `end` = right.
- In a right-to-left language (Arabic, Hebrew), `start` = right, `end` = left.

This makes `start` and `end` more flexible for internationalization than `left` and `right`.

Another common value is `justify`, which stretches text so that both the left and right edges of each line line up evenly. (The last line of a paragraph is normally left as-is unless you also set `text-align-last`.) This can look neat in print, but on the web it often creates uneven gaps between words ("rivers of white space"), especially when the container is narrow or the text is short. Use it sparingly, and test carefully.

---

## 7. `line-height` (A Brief Note)

`line-height` controls the vertical space between lines of text. It is not strictly a font property, but it has a major effect on readability and is commonly set alongside `font-size`.

```css
p {
  font-size: 16px;
  line-height: 1.6; /* computed value remains the number 1.6; used value = 1.6 × 16px = 25.6px */
}
```

A unitless value like `1.6` is relative to the element's own `font-size`, which makes it scale predictably. For body text, values between `1.4` and `1.8` are typical. We will use `line-height` in the final example.

> **Why unitless?** If you wrote `line-height: 25.6px`, the line height would stay fixed even if the font size changed. A unitless value recalculates whenever the font size changes, which is almost always what you want.

---

## 8. Putting It All Together

The following example applies everything from this guide to a small, realistic page. Read the comments to see how each property contributes.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Font Properties Demo</title>

  <!-- Load a custom web font with the 400 and 700 weights used below.
      The generic fallback (cursive) is included in the font-family rule. -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Caveat:wght@400;700&amp;display=swap">

  <style>
    /* Root font size: every rem value is relative to this. */
    html {
      font-size: 16px;
    }

    body {
      /* Body text: a readable serif with sensible fallbacks. */
      font-family: Georgia, "Times New Roman", serif;
      font-size: 1rem;      /* 16px */
      font-weight: 400;     /* normal */
      font-style: normal;   /* upright */
      line-height: 1.6;     /* 1.6 × 16px = 25.6px */
      text-align: start;    /* left in LTR languages */
    }

    h1 {
      /* Headline: a custom handwriting font with a cursive fallback. */
      font-family: "Caveat", cursive;
      font-size: 2.5rem;    /* 2.5 × 16px = 40px, regardless of parent */
      font-weight: 700;     /* bold */
      font-style: normal;   /* upright */
      line-height: 1.2;     /* tighter spacing is fine for headlines */
      text-align: center;
    }

    .byline {
      /* Small metadata line: lighter weight, smaller size, italic. */
      font-size: 0.875rem;  /* 0.875 × 16px = 14px */
      font-weight: 300;
      font-style: italic;
      text-align: center;
    }

    blockquote {
      /* Right-aligned italic quote. */
      font-size: 1.125rem;  /* 18px */
      font-style: italic;
      line-height: 1.5;
      text-align: right;
    }
  </style>
</head>
<body>
  <h1>A Short Guide to CSS Typography</h1>
  <p class="byline">By A. Beginner</p>

  <p>
    This paragraph uses the body styles defined above: a 16px serif font with a
    line height of 1.6. The result is comfortable to read on most screens.
  </p>

  <blockquote>
    Good typography is invisible; bad typography is everywhere.
  </blockquote>
</body>
</html>
```

Notice how each decision maps back to a principle from earlier sections:

- `font-size: 1rem` on the body ties all body text to the root size, so changing `html { font-size: ... }` rescales the whole page predictably.
- `font-family` ends in a generic family (`serif` or `cursive`) so text stays readable even if the preferred font fails to load.
- `font-weight: 300` on the byline only looks different if the chosen font actually ships a 300 weight; otherwise the browser substitutes the closest available weight. This is also why the demo explicitly loads Caveat's 700 weight for the `h1`.
- `font-style: italic` on the byline uses the font's italic version if available; otherwise the browser synthesizes a slanted version.
- `line-height: 1.6` is unitless, so it recalculates if the font size changes.
- `text-align: start` adapts automatically to right-to-left languages.

---

## 9. Quick Reference

| Property | Purpose | Common values |
|----------|---------|---------------|
| `font-size` | Size of text | `16px`, `1.2rem`, `12pt`, `large` |
| `font-weight` | Thickness of strokes | `normal`, `bold`, `100`–`900`, `lighter`, `bolder` |
| `font-style` | Italic vs. upright | `normal`, `italic`, `oblique` |
| `font-family` | Typeface | `"Helvetica Neue", Arial, sans-serif` |
| `text-align` | Horizontal alignment | `left`, `right`, `center`, `start`, `end` |
| `line-height` | Vertical space between lines | `1.5`, `24px`, `normal` |

---

## 10. Key Takeaways

1. **Use `rem` for font sizes.** It gives you a single point of control (the root size) and avoids the compounding confusion of nested `em` values.
2. **Always provide font fallbacks.** End every `font-family` list with a generic family so text stays readable even when your preferred font is unavailable.
3. **Load custom fonts through a service like Google Fonts** when you need a typeface that users may not have installed — and always keep the generic fallback in place in case the request fails.
4. **Prefer unitless `line-height`.** It scales with the font size, which is almost always what you want.
5. **Remember that `font-weight` depends on the font.** A weight value only produces a visible difference if the font actually provides that weight.
6. **Use `start` and `end` for alignment** when you want your layout to adapt to right-to-left languages.
7. **Understand what `em` is relative to.** On `font-size`, it looks to the parent; on other properties, it looks to the element's own computed font size. When in doubt, `rem` is simpler.
8. **Keep `font-style` and `font-weight` distinct in your mind.** `font-style` controls slant (italic); `font-weight` controls thickness. They are different properties and can be combined freely.
