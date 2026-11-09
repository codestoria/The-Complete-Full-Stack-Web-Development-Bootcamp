# The CSS Box Model: Margin, Padding, and Border

## Contents

1. [Why the Box Model Matters](#1-why-the-box-model-matters)
2. [The Four Layers of a Box](#2-the-four-layers-of-a-box)
3. [Width and Height: Sizing the Content Box](#3-width-and-height-sizing-the-content-box)
4. [Border](#4-border)
5. [Padding](#5-padding)
6. [Margin](#6-margin)
7. [Individual Sides and the One-to-Four-Value Shorthand](#7-individual-sides-and-the-one-to-four-value-shorthand)
8. [The Sizing Trap: `content-box` vs. `border-box`](#8-the-sizing-trap-content-box-vs-border-box)
9. [Grouping Elements with `<div>`](#9-grouping-elements-with-div)
10. [Worked Example: Three Boxes Side by Side](#10-worked-example-three-boxes-side-by-side)
11. [Summary](#11-summary)

---

## 1. Why the Box Model Matters

When you look at a web page, you see text, images, and colored areas. To the browser, however, every one of those things is a **rectangular box**. Even an element with no visible border or background still occupies a box on the page—and that box determines where the element sits and how much space it takes up.

This is the single most important idea in CSS layout: **every HTML element is a box**.

The rules that govern that box—its size, its spacing, and its borders—are what we call the **box model**. Once you understand the box model, you can predict why elements appear where they do. More importantly, you can fix them when they don't. In practice, most "why is this element in the wrong place?" bugs come down to one of the box's four layers behaving differently from what you expected. Learning to see the box model is therefore the first step toward reliable layout.

## 2. The Four Layers of a Box

The box model describes the four layers that make up every element's box, from the inside out:

```
┌───────────────────────────────────────┐
│                Margin                 │
│  ┌─────────────────────────────────┐  │
│  │             Border              │  │
│  │  ┌───────────────────────────┐  │  │
│  │  │          Padding          │  │  │
│  │  │  ┌─────────────────────┐  │  │  │
│  │  │  │       Content       │  │  │  │
│  │  │  │  (width × height)   │  │  │  │
│  │  │  └─────────────────────┘  │  │  │
│  │  └───────────────────────────┘  │  │
│  └─────────────────────────────────┘  │
└───────────────────────────────────────┘
```

- **Content** — the actual text, image, or child elements. Its size is set by `width` and `height`.
- **Padding** — space *inside* the border, between the content and the border.
- **Border** — a visible (or invisible) line drawn around the padding.
- **Margin** — space *outside* the border, between this element and its neighbors.

The rest of this guide examines each layer in turn. As you read, keep one question in mind: **when I change this property, does the element's total footprint grow, or does the space inside it change?** That distinction is the key to predicting layout.

Before we look at each layer, it helps to understand a fundamental distinction: the **specified size** of a box (what you write in CSS) and its **actual footprint** (the space it really occupies on the page) are not always the same number. That gap is the source of most layout surprises.

## 3. Width and Height: Sizing the Content Box

```css
.box {
  width: 200px;
  height: 300px;
}
```

By default (`box-sizing: content-box`), `width` and `height` set the size of the **content box**—the innermost layer, where the text or image lives. (This applies to block-level and replaced elements, and to inline-block boxes; in flex and grid layout, `width` and `height` generally apply to flex items and grid items as well. On non-replaced inline elements such as `<span>`, `width` and `height` generally do not apply—see [§9](#9-grouping-elements-with-div).) You can use absolute units like pixels for fixed sizes, or percentages for sizes relative to the containing block (often, but not always, the parent element):

```css
.full-width {
  width: 100%; /* fills the containing block's width */
}
```

> **Important:** By default, `width` and `height` refer to the *content box only*. Padding and border are added **on top** of these dimensions. This is a common source of confusion, so let's make it concrete: an element with `width: 200px`, `20px` padding on each side, and a `10px` border on each side has a **260px** border-box width, not 200px. (Throughout this guide, “total width” means this border-box width—content + padding + border—and excludes margin. To get the full space the element takes up in layout, add its horizontal margins as well.) We'll return to this in [§8](#8-the-sizing-trap-content-box-vs-border-box) and show how to change the behavior if you prefer.

## 4. Border

The `border` property is a shorthand that typically combines three components (any of which may be omitted):

```css
.box {
  border: 10px solid black;
}
```

The three components are:

1. **Border width (thickness)** — e.g., `10px`, `2px`, `thin`.
2. **Style** — e.g., `solid`, `dashed`, `dotted`, `double`.
3. **Color** — any named color, hex code, or RGB value.

The conventional order is width, then style, then color, and you should write it that way for readability. However, CSS does not enforce a fixed order for these three components. `border: solid 10px black` is equally valid, because the values are unambiguous: only one value is parsed as the border width (a length or a width keyword such as `thin`), only one as the style, and only one as the color. The browser parses each value by its type, not by its position.

Changing the border width makes the line thicker or thinner:

```css
border: 30px solid black;
```

Changing the style to `dashed` gives a dashed line:

```css
border: 10px dashed black;
```

> **Crucial behavior (default `content-box` sizing):** When the element has an explicit `width` or `height`, increasing the border thickness does **not** change that specified size. The border grows **outward** from the content box: the content area stays the same size, but the element's total footprint increases. If `width` is `auto` (as with a default block-level element), the opposite happens for horizontal borders—the box still fills its container, so the content area shrinks and the outer width stays the same. (If you use `box-sizing: border-box` and the element has an explicit `width`, the border is counted inside that width, so the content area shrinks instead—see [§8](#8-the-sizing-trap-content-box-vs-border-box).)

> **Tip:** `border-radius` rounds the corners of the border, e.g., `border-radius: 6px`. It is often combined with a border to create card-like components.

## 5. Padding

**Padding is space inside the border**, between the content and the border.

```css
.box {
  padding: 20px;
}
```

With a `10px` border and `20px` padding, the visual result is:

- The content area (e.g., text) sits in the middle.
- 20px of empty space surrounds the content on all sides.
- Then the 10px border.
- Then the margin (outside).

Under the default `content-box` sizing, when the element has an explicit `width` or `height`, padding pushes the border outward from the content: the content area keeps its specified size, and the element's total size increases. With `box-sizing: border-box` and a fixed width, adding padding reduces the content area instead; the border does not move outward.

> **Note:** If you do not set a width on a block-level element, it fills the available width of its container. In that case, adding horizontal padding does not make the box wider; instead, the content area shrinks. (If padding and border together already exceed the container width, the box can still overflow—but in ordinary auto-width cases it stays within the container.)

The effect of padding is most visible when the element contains text: without padding, text touches the border; with padding, there is comfortable space between them. Padding also extends the element's background color, which is why backgrounds often appear to "fill" the space around text.

Padding accepts the same one-to-four-value clockwise shorthand as `border-width` (covered in [§7](#7-individual-sides-and-the-one-to-four-value-shorthand)).

## 6. Margin

**Margin is space outside the border**, between this element and other elements.

```css
.box {
  margin: 10px;
}
```

If two elements sit side by side, each with `margin: 10px` on the left and right, the gap between them is `20px` (10px from each). Margin creates space *between* elements in a way that padding cannot.

> **Margin collapse:** When two vertical margins meet—for example, the bottom margin of one block and the top margin of the next—they *collapse* into a single margin. If both margins are positive, the result is the larger of the two, not their sum. If one block has `margin-bottom: 20px` and the next has `margin-top: 10px`, the gap between them is `20px`, not `30px`. Negative margins follow more complex rules: when one margin is positive and one is negative, the result is their sum (so `20px` and `-10px` collapse to `10px`); when both are negative, the result is the more negative value. Horizontal margins do not collapse. For now, be aware that collapse exists; it mainly affects vertical spacing between block-level elements.

Margin also accepts the clockwise shorthand:

```css
margin: 0 10px;      /* 0 top & bottom, 10px left & right */
margin: 20px 0 0 0;  /* 20px top only */
margin-left: 260px;  /* with a fixed width, moves the box 260px right
                        (assuming the container is wide enough);
                        with width: auto, the left edge shifts right and
                        the content width shrinks, usually keeping the
                        right edge against the container; it overflows
                        when space runs out */
```

> **Tip:** `margin: 0 auto;` horizontally centers a block-level element in normal flow whose used width is narrower than its container. The `auto` value tells the browser to split the remaining horizontal space equally between the left and right margins. The width can come from an explicit `width` (as in the `.card` example later in this guide) or from a `max-width` constraint. Note that this does not work on floated elements, and `auto` margins on inline elements compute to `0`. For absolutely positioned elements, auto margins can center the element only when the `left`, `right`, and `width` constraints allow the auto margins to participate in the equation.

## 7. Individual Sides and the One-to-Four-Value Shorthand

So far we've set padding, border width, and margin for all four sides at once. CSS also lets you target a single side with a side-specific property:

```css
.box {
  border: 30px solid black;
  border-top-width: 0px;
}
```

The second declaration overrides the top border width to `0`, removing the top border. **Order matters**: the override must come *after* the general shorthand declaration. If the declarations are in separate rules, the later rule wins when specificity is equal.

> **A brief note on specificity:** Not all rules are equal. A selector like `#header .box` is more specific than `.box`, so it wins even if it appears earlier. DevTools shows overridden rules with a strikethrough, which is the easiest way to see which rule actually applies. For now, remember: when two rules conflict, the more specific selector wins; if they are equally specific, the later rule wins.

### The Multi-Value Shorthand: Clockwise Order

`border-width`, `padding`, and `margin` all accept **one to four values**, applied in **clockwise order** starting from the top:

| Number of values | Order of application |
|------------------|----------------------|
| 1 value | All four sides |
| 2 values | Top & bottom, then left & right |
| 3 values | Top, then left & right, then bottom |
| 4 values | Top, right, bottom, left |

Examples:

```css
/* All sides 10px */
border-width: 10px;

/* Top & bottom 10px, left & right 20px */
border-width: 10px 20px;

/* Top 10px, left & right 20px, bottom 30px */
border-width: 10px 20px 30px;

/* Top 10px, right 20px, bottom 30px, left 40px */
border-width: 10px 20px 30px 40px;
```

The same pattern applies to `padding` and `margin`. This shorthand is worth memorizing: it appears constantly in real-world CSS.

## 8. The Sizing Trap: `content-box` vs. `border-box`

By default, `width` and `height` refer to the **content box**. If you set `width: 200px` and then add `20px` padding and a `10px` border on each side, the element actually occupies:

```
200px (content) + 20px (left padding) + 20px (right padding)
                + 10px (left border)  + 10px (right border)
= 260px total width
```

This surprises many people. To make `width` include padding and border, use:

```css
*,
*::before,
*::after {
  box-sizing: border-box;
}
```

Many developers apply this rule globally at the start of every project. It makes sizing far more intuitive: `width: 200px` means the element is 200px wide *including* padding and border.

> **Note:** With `border-box`, when the element's `width` is fixed, increasing padding or border thickness reduces the content area instead of increasing the element's total width. This is the key behavioral difference from `content-box`. If padding and border together exceed the specified width, the content area is clamped at 0 and the element's actual outer width can exceed the specified width.

> **Why is `content-box` the default?** Because it was the original CSS behavior, and changing it would break existing websites. `border-box` was added later as an opt-in because it matches how designers and developers usually think about sizing ("200px means 200px").

This difference is the source of real-world problems. Suppose you want a box to take up exactly 200px in a 1000px-wide container. You write `width: 200px`, then add `padding: 20px` and a `10px` border on each side for readability. The box now occupies 260px—too big for its intended slot. That is why many developers set `box-sizing: border-box` from the start.

## 9. Grouping Elements with `<div>`

HTML elements like `<p>` and `<img>` create boxes automatically. But sometimes you want to **group several elements together** into one box so you can style or position them as a unit. For this, use the `<div>` element—short for "division."

```html
<div class="card">
  <img src="photo.jpg" alt="A photo">
  <p>A caption for the photo.</p>
</div>
```

A `<div>` has **a transparent background and no border by default** (its `background` is transparent, and its `border-style` defaults to `none`). It still participates in layout as a block-level box, but its only purpose is to act as a container. You give it meaning and appearance through CSS:

```css
.card {
  border: 1px solid #ccc;
  padding: 16px;
  margin: 20px;
  width: 300px;
}
```

Divs are essential for layout: you can wrap sections, apply a shared background, or position a group of elements together.

Because divs are invisible, debugging layout issues involving them can be hard. This is where a tool like **Pesticide** (a free Chrome extension) helps: it outlines every element on the page with a colored dashed line, so you can see the boxes, their nesting, and their sizes. To inspect an element's tag name, classes, and IDs, use the browser's DevTools.

> **Note:** By default, `<div>` elements are **block-level**: they start on a new line and take up the full width available. Other elements, like `<span>`, are **inline**: they sit within the flow of text. We will not cover `display` in depth here, but the box model applies to both types. For non-replaced inline elements, however, `width` and `height` usually do not apply, vertical padding and borders do not increase the line box height, and vertical margins do not affect line layout.

## 10. Worked Example: Three Boxes Side by Side

Suppose you want three `div` boxes, each with a `200px × 200px` content area, arranged horizontally. Each box has `20px` padding and a `10px solid black` border.

**Step 1: Calculate each box's total width.**

Under the default `content-box` sizing, the total horizontal space each box occupies is:

```
200px (content) + 20px + 20px (padding) + 10px + 10px (border) = 260px
```

So each box takes up 260px, not 200px. Keep this figure in mind whenever you need to know how much room a box really needs.

**Step 2: Put the boxes in a row.**

By default, `<div>` elements are block-level and stack vertically. To place them side by side, set `display: inline-block` (or use a modern layout system such as Flexbox, covered elsewhere). With `inline-block`, the boxes flow in document order, like words on a line: Box 2 naturally sits to the right of Box 1, and Box 3 to the right of Box 2. **You do not need to add `margin-left` to Box 2 or Box 3 just to make them follow one another.** Normal flow already handles that.

**Step 3: Watch out for whitespace between `inline-block` elements.**

A subtle detail: any whitespace in the HTML between `inline-block` elements (a newline, a space, indentation) renders as a visible gap roughly one space character wide. With three boxes there are two such gaps, so a container whose width is exactly `3 × 260px` (780px) no longer has room for all three when those gaps are present—the gaps are enough to push the third box onto a new line. If you want the three boxes to touch exactly, either:

- remove the whitespace between the tags in your HTML, or
- use Flexbox, which ignores whitespace between items.

If you want a deliberate gap between the boxes, add `margin`—but that is your design choice, not something you compute from the previous box's width.

**When *would* you calculate the previous box's width?**

You would typically do this only when positioning an element manually, outside normal flow—for example, with `position: absolute`. In the simplest case—both boxes share the same positioning context, the second box has no `margin-left` of its own, and the container has no `padding-left`—you can approximate the offset by adding the previous box's **border-box width** to its `margin-right`. Be aware, though, that `left` is measured from the absolutely positioned element's containing block (normally the **padding box** of its nearest positioned ancestor), not from the previous sibling, while normal-flow siblings start from the container's **content box**. As soon as any of those extra offsets (the container's `padding-left`, the element's own `margin-left`, etc.) is present, the simple width-plus-`margin-right` figure is no longer enough and must be adjusted. In normal flow (block, inline, or inline-block), the browser handles placement, and the only calculation you need is whether the container has enough room for all the boxes.

The core lesson: **under default `content-box` sizing, a box's total width is `content + padding + border`.** Forgetting padding and border is one of the most common reasons a layout overflows its container or wraps unexpectedly.

## 11. Summary

### Margin vs. Padding

| | Margin | Padding |
|--|--------|---------|
| Location | Outside the border | Inside the border |
| Purpose | Space *between* elements | Space between content and border |
| Affects background? | No (transparent) | Yes (background extends through padding) |
| Can be negative? | Yes (pulls elements closer) | No |
| Collapses vertically? | Yes—between adjacent block-level siblings in normal flow, and in certain parent–child and empty-block cases; not in flex/grid or other non-collapsing contexts | No |

A helpful mental model:

- **Padding** is like the cushioning inside a parcel: it keeps the contents away from the walls of the box.
- **Border** is the wall of the box.
- **Margin** is the space you leave between this parcel and other parcels on the shelf.

### Quick Reference: Box Model Properties

| Property | Values | Notes |
|----------|--------|-------|
| `width` / `height` | `px`, `%`, `rem` | Content size by default |
| `padding` | 1–4 values, clockwise | Inside the border |
| `border` | `thickness style color` (order not enforced) | Under `content-box`, grows outward when width/height is fixed; with `width: auto`, the content area may shrink instead |
| `border-width` | 1–4 values, clockwise | Sets all four border widths; use `border-*-width` for a single side. Placed after `border`, it overrides those widths. |
| `margin` | 1–4 values, clockwise | Outside the border; can be negative |
| `box-sizing` | `content-box` (default), `border-box` | `border-box` makes width include padding + border |

> **Key takeaway:** Every element is a box with four layers. Learn to calculate total width as `content + padding + border` (under default sizing), and remember that `margin` controls the space between boxes, not the box's own size.
