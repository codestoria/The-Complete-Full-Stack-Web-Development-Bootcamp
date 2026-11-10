# CSS Float: Wrapping Text Around Elements

## Contents

1. [Introduction: Why Learn Float?](#1-introduction-why-learn-float)
2. [The Baseline: Normal Document Flow](#2-the-baseline-normal-document-flow)
3. [The `float` Property](#3-the-float-property)
4. [The Problem: Unwanted Wrapping and How `clear` Fixes It](#4-the-problem-unwanted-wrapping-and-how-clear-fixes-it)
5. [Putting It Together: A Complete Example](#5-putting-it-together-a-complete-example)
6. [Important: Float Is Not a General Layout Tool](#6-important-float-is-not-a-general-layout-tool)
7. [Summary](#7-summary)

---

## 1. Introduction: Why Learn Float?

Layout is at the heart of web design. Before you can build a webpage that looks the way you intend, you need to understand how elements are positioned on the page. One of the classic tools for controlling layout in CSS is the `float` property.

The primary purpose of `float` is simple but powerful: **it allows text to wrap around a particular element** — most commonly, an image. This pattern is borrowed directly from print media like newspapers and magazines, where text flows around images and other visual elements.

In this tutorial, you will learn:

- What the `float` property does and how it interacts with normal document flow
- How to use `float: left` and `float: right`
- Why floats can cause layout problems (and how `clear` fixes them)
- When to use `float` — and when **not** to use it

This tutorial assumes you know basic HTML and CSS syntax. No prior knowledge of layout techniques is required.

---

## 2. The Baseline: Normal Document Flow

Before we introduce `float`, we need to understand what happens **without** it.

Every HTML element participates in something called **normal document flow**. By default:

- **Block-level elements** (such as `<div>`, `<p>`, `<h1>`, and `<section>`), when `width` is `auto` (the default), take up the full width available to them, even if their content does not need all that space. They stack vertically, one after another, each starting on a new line.
- **Inline elements** (such as `<span>`, `<a>`, and `<img>`) sit next to each other on the same line, taking up only as much width as their content requires.

### Where Does `<img>` Fit?

`<img>` is an **inline-level replaced element**. It sits on a text line like an inline element, but its size is determined by its intrinsic dimensions and/or its `width` and `height` (set via HTML attributes or CSS), rather than by surrounding text.

Consider this HTML:

```html
<img src="cat.jpg" alt="A cat">
<p>A paragraph of text that should appear next to the image.</p>
```

What happens without any CSS?

- The `<img>` is inline, so it sits on a line. In this example, because the following `<p>` is block-level, the image effectively appears on a line by itself.
- The `<p>` is block-level, so it always starts on a new line — **below** the image.
- The paragraph text never appears next to the image.

Even if you place the image inside the paragraph:

```html
<p><img src="cat.jpg" alt="A cat"> A paragraph of text...</p>
```

The text begins next to the image on the first line, but any text that wraps to a second line starts **below the line box containing the image**, not beside the image. You do not get the clean, magazine-style wrapping that `float` provides.

> **Key idea:** `float` exists to break an element out of normal flow so that other content can flow around it.

---

## 3. The `float` Property

### Syntax

```css
selector {
  float: left | right | none;
}
```

- `left` — pushes the element to the left edge of its containing block; subsequent inline content wraps on the right.
- `right` — pushes the element to the right edge; subsequent inline content wraps on the left.
- `none` — the default value; the element stays in normal flow.

### What Happens When You Apply `float`

When you apply `float` to an element:

1. The element is **taken out of normal document flow**. Its parent’s normal-flow height calculation generally no longer includes it.
2. The element is pushed to the **left or right edge** of its containing block (the nearest block-level ancestor).
3. The element no longer stretches to fill the available width. **A non-replaced floated element with `width: auto` shrinks to fit its content; if you set a `width`, that width is used.** **Replaced elements** such as `<img>` keep their intrinsic dimensions or any `width`/`height` you set.
4. **Subsequent inline content** (text, inline images, links) **flows around it**, filling the space on the opposite side.

Point 4 is the key to understanding float, but there is an important distinction: **only line boxes are shortened to flow around a float; the block-level boxes themselves are not moved**. Ordinary block-level boxes that do not establish a new block formatting context (such as `<p>` or `<footer>`) do not shift their own border boxes to avoid the float — their boxes still start at the left edge of the containing block. However, the **line boxes** inside them are shortened so that their text flows around the float. This distinction explains many float-related problems, as you will see in Section 4.

### Example: Floating an Image to the Left

Suppose you have the following HTML:

```html
<img src="example.jpg" alt="An example image">
<p>
  Lorem ipsum dolor sit amet, consectetur adipiscing elit. ...
</p>
```

By default, the paragraph appears below the image. Now add this CSS:

```css
img {
  float: left;
}
```

**Result:** The image moves to the left edge of its container, and the paragraph text wraps around the image on the right side. Each line of text that fits beside the image begins at the image's right edge; only when the text extends below the image does it span the full width.

Notice something important: **we did not change anything about the paragraph**. We only set `float` on the image. The wrapping behavior is a side effect of removing the image from normal flow. The paragraph still behaves like a normal block — its box starts at the left edge of the container — but its text lines find space beside the floating image.

### Example: Floating to the Right

You can also float an element to the right:

```css
img {
  float: right;
}
```

**Result:** The image moves to the right edge, and the text wraps around it on the left side.

### Multiple Floats

You can float multiple elements in the same container. For example, with two images, you could float one left and the other right:

```css
img:first-of-type {
  float: left;
}

img:last-of-type {
  float: right;
}
```

The text between them wraps around both floating images, creating a column of text down the middle. If there is not enough horizontal space for both floats on the same line, the second floated element is pushed down. If two floats are on the same side and would overlap, the later one is also pushed down.

---

## 4. The Problem: Unwanted Wrapping and How `clear` Fixes It

Floats are powerful, but they can cause unexpected behavior when you do not want **everything** to wrap around them.

### The Footer Problem

Imagine this structure:

```html
<img src="example.jpg" alt="...">
<p>Some paragraph text...</p>
<footer>© 2024 My Company</footer>
```

You float the image to the left:

```css
img {
  float: left;
}
```

The paragraph wraps around the image — that is what you wanted. But now look at the footer. The footer is a block-level element, so its box starts at the left edge of the container. Because the floated image is still active, the footer's text lines also try to wrap around the image. If the image is tall enough, the footer text appears squeezed beside the image instead of below it. Visually, the copyright notice ends up in a strange position, not at the bottom of the page where you would expect it.

This happens because the float is **persistent**. The image is still taken out of the flow, and **subsequent line boxes** are affected by it until the content is cleared or extends past the float.

### The Fix: The `clear` Property

To prevent an element from wrapping around a float, you use the `clear` property.

```css
selector {
  clear: left | right | both | none;
}
```

- `clear: left` — the element's border edge is pushed down below any left-floated elements, so it does not sit beside them.
- `clear: right` — the element's border edge is pushed down below any right-floated elements.
- `clear: both` — the element is pushed down below both left- and right-floated elements. This is the most common value.
- `clear: none` — the default; no clearing is applied.

**Important:** `clear` applies to **block-level boxes**. Setting it on an element whose principal box remains inline-level (such as an inline `<span>`) has no effect.

### Applying `clear`

Back to our footer problem:

```css
footer {
  clear: both;
}
```

Now the footer **ignores** the floating image entirely and positions itself below it. It no longer tries to squeeze into the remaining space beside the image.

> **How to think about it:** `clear` tells an element: "Do not let your border edge sit beside a floating element. Start below it instead."

You can also use `clear: left` or `clear: right` if you only need to clear floats in one direction. But `clear: both` is the safest and most common choice when you want an element to sit below all floats.

---

## 5. Putting It Together: A Complete Example

Let's walk through a realistic layout to see `float` and `clear` in action.

### The Goal

We want:

- Two content blocks side by side (one on the left, one on the right)
- Each block containing an image with text wrapping around it
- A footer spanning the full width below both blocks

### The HTML Structure

```html
<div class="cat-block">
  <img src="cat.jpg" alt="A cat">
  <p>Some text about cats...</p>
</div>

<div class="dog-block">
  <img src="dog.jpg" alt="A dog">
  <p>Some text about dogs...</p>
</div>

<footer>© 2024 My Company</footer>
```

### Step 1: Float the Images

First, make the text wrap around the images:

```css
img {
  float: left;
}
```

Both images now float left, and their respective paragraph text wraps around them.

### Step 2: Float the Blocks Side by Side

Next, position the two blocks:

```css
.cat-block,
.dog-block {
  width: 45%;
}

.cat-block {
  float: left;
}

.dog-block {
  float: right;
}
```

Now the cat block sits on the left side of the page, and the dog block sits on the right side. Because their combined widths fit within the container, they line up side by side.

### Step 3: Clear the Footer

But wait — the footer's text lines are now trying to wrap around both floating blocks. It might appear in the narrow space between them, which is not what we want. We need to clear it:

```css
footer {
  clear: both;
}
```

Now the footer pushes down below both floating blocks, spanning the full width of the page. The layout is complete.

### Two Float Side Effects Worth Knowing

This layout looks correct, but floats bring two issues worth understanding:

1. **Parent height collapse.** Floated blocks are removed from normal flow, so their parent container does not extend vertically to contain them. In this example, the footer's `clear: both` pushes the footer below the floating blocks, which makes the overall layout look correct. But the parent container itself does not contain the floated blocks — if the footer were removed or moved, the parent could collapse to zero height and the floated blocks would stick out below it. In modern code, if an element needs to contain floating children, you can use `display: flow-root` to make the container include them.

2. **Unequal heights.** The two blocks may have different heights (due to different amounts of text). Floats do not equalize the heights of side-by-side elements — each block is only as tall as its content. A gap may appear below the shorter block.

These limitations are why float is not suitable for layout, and why modern tools exist.

---

## 6. Important: Float Is Not a General Layout Tool

Here is a critical point that many beginners miss: **in modern web development, you should not use `float` for general page layout.**

### A Brief History

In the past, before better tools existed, developers used `float` extensively to build complex multi-column layouts. They "wrangled" floats, clears, and overflow tricks to position everything. It was possible — but it was fragile, unpredictable, and often produced strange results.

### The Modern Approach

Today, we have much better tools for layout:

- **Flexbox** — for one-dimensional layouts (rows or columns)
- **CSS Grid** — for two-dimensional layouts
- **Frameworks like Bootstrap** — which provide pre-built grid systems based on Flexbox or Grid

These tools are easier to use, more predictable, and designed specifically for layout. They handle equal heights, gaps, and responsive behavior automatically.

### When to Use Float

So when **should** you use `float`?

> **Use `float` primarily for one thing: wrapping text around an image (or a similar inline-level replaced element).**

This is where `float` works best. It is simple, well-supported, and does exactly what you need. For everything else — columns, grids, sidebars — use Flexbox or Grid.

You will still see `float` in older codebases, so it is important to understand how it works. But for new projects, treat `float` as a specialized tool, not a general-purpose layout solution.

---

## 7. Summary

| Concept | What It Does | When to Use It |
|---------|-------------|----------------|
| `float: left` | Pushes an element to the left; content wraps on the right | Wrap text around an image on the left |
| `float: right` | Pushes an element to the right; content wraps on the left | Wrap text around an image on the right |
| `clear: left` | Pushes the element below left floats | Push an element below a left-floated image |
| `clear: right` | Pushes the element below right floats | Push an element below a right-floated image |
| `clear: both` | Pushes the element below all floats | Safest choice for footers and full-width elements |

### Key Takeaways

1. **Normal flow** stacks block-level elements vertically and places inline elements on a line. `float` breaks an element out of this flow so that other content can wrap around it.
2. **`float: left` and `float: right`** are the two values you will use in practice. They push an element to one side and let inline content flow around it.
3. **Floats affect subsequent line boxes** in the same formatting context until they are cleared or the content extends past the float. Use `clear` to stop unwanted wrapping.
4. **`clear: both`** is the safest choice for elements that should sit below all floats, such as footers.
5. **Do not use `float` for layout.** Use it for text wrapping around images. Use Flexbox or Grid for everything else.

With these fundamentals, you are ready to use `float` effectively — and to recognize when a different tool is the right choice.
