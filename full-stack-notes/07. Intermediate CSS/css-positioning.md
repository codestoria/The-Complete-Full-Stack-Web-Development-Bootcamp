# CSS Positioning

## Contents

1. [Introduction: Why Do We Need Positioning?](#introduction-why-do-we-need-positioning)
2. [The `position` Property at a Glance](#the-position-property-at-a-glance)
3. [Static Positioning: The Default](#static-positioning-the-default)
4. [Relative Positioning: Moving from the Original Spot](#relative-positioning-moving-from-the-original-spot)
5. [Absolute Positioning: Relative to a Positioned Ancestor](#absolute-positioning-relative-to-a-positioned-ancestor)
6. [Fixed Positioning: Pinned to the Viewport](#fixed-positioning-pinned-to-the-viewport)
7. [The Offset Properties: `top`, `right`, `bottom`, `left`](#the-offset-properties-top-right-bottom-left)
8. [How Positioning Interacts with the Box Model](#how-positioning-interacts-with-the-box-model)
9. [Stacking and `z-index`](#stacking-and-z-index)
10. [Hands-On: Drawing a Circle and Placing It Inside a Rectangle](#hands-on-drawing-a-circle-and-placing-it-inside-a-rectangle)

---

## Introduction: Why Do We Need Positioning?

By default, HTML elements are laid out in what is called **normal flow**. Block-level elements stack vertically, one after another. Inline elements flow horizontally along a line of text. The browser decides where everything goes based on the document structure and the CSS box model. Most of the time, this automatic layout is exactly what you want.

But sometimes normal flow is not enough. Consider these common design requirements:

- You want to nudge one element a few pixels away from where it would normally sit, without affecting the elements around it.
- You want to place a notification badge in the top-right corner of a card, regardless of what else is inside the card.
- You want a navigation bar to stay pinned to the top of the screen while the page scrolls.

Normal flow alone cannot handle these cases cleanly. You need a way to take an element out of the normal layout or shift it relative to a specific reference point. That is exactly what the CSS `position` property is for.

This guide covers the four core values of `position` — `static`, `relative`, `absolute`, and `fixed` — and explains how each one changes the rules of layout. It also covers the offset properties (`top`, `right`, `bottom`, `left`) that control *where* a positioned element goes, and `z-index`, which controls which element appears on top when elements overlap.

> **Note:** CSS also has a fifth value, `sticky`, which combines aspects of relative and fixed positioning. It is beyond the scope of this guide, but once you understand the four values here, `sticky` will be much easier to learn.

---

## The `position` Property at a Glance

The `position` property accepts four common values:

```css
position: static;  /* default */
position: relative;
position: absolute;
position: fixed;
```

Each value answers a different question: **"What is this element positioned relative to?"**

| Value | Positioned relative to | Remains in normal flow? |
|---|---|---|
| `static` | Nothing — it simply follows normal flow | Yes |
| `relative` | Its own original position in normal flow | Yes (its original space is preserved) |
| `absolute` | The nearest *positioned ancestor* (or the page's top-left corner if none exists) | No (removed from flow) |
| `fixed` | The browser viewport | No (removed from flow) |

Any element whose `position` value is **not** `static` is called a **positioned element**. This distinction matters because positioned elements can serve as reference points for absolutely positioned descendants — a concept we will explore in [Section 5](#absolute-positioning-relative-to-a-positioned-ancestor).

The offset properties (`top`, `right`, `bottom`, `left`) only take effect when `position` is **not** `static`. Among the four values covered in this guide, that means `relative`, `absolute`, and `fixed`. We will look at those in [Section 7](#the-offset-properties-top-right-bottom-left).

---

## Static Positioning: The Default

Every HTML element has `position: static` by default. You never need to write it; the browser applies it automatically.

```css
.box {
  /* position: static; is applied by default */
}
```

With static positioning, elements behave exactly as you have seen so far: each block-level element is placed below the previous one, and the browser computes the layout from the normal flow. There is no special reference point, no removal from flow, and no overlap.

A key consequence: **offset properties do nothing on a static element.**

```css
.box {
  position: static;
  top: 50px;    /* ignored */
  left: 50px;   /* ignored */
}
```

The element will not move. This is a common source of confusion for beginners: writing `top` and `left` without changing `position` has no visible effect. The reason is that `static` elements do not establish a positioning context, so there is no reference point for the offsets to act against.

**Why this matters:** If you ever set `top`, `left`, `right`, or `bottom` and nothing happens, the first thing to check is whether the element has a non-static `position`. This one habit will save you a lot of debugging time.

---

## Relative Positioning: Moving from the Original Spot

`position: relative` keeps the element in the normal flow but allows you to shift it **relative to where it would have been**.

```css
.box {
  position: relative;
  top: 50px;
  left: 50px;
}
```

Here is what happens, step by step:

1. The browser first lays out the element in its normal, static position.
2. Then it shifts the element 50px down and 50px to the right from that original position.

Crucially, **the space the element originally occupied is still reserved.** Other elements do not move up to fill the gap. Visually, the element is shifted, but the layout still treats it as if it were in its original spot. This is why relative positioning is often described as "visual displacement without layout disruption."

### The most important thing to remember

`relative` does **not** mean "relative to another element" or "relative to the parent." It means **relative to the element's own original position**.

This is one of the most common misconceptions. If you have a box in the middle of the page and you set `position: relative; top: 50px;`, the box moves 50px down from where it already was — not 50px from the top of the page, and not 50px from its parent.

### A simple mental model

Think of relative positioning as picking up a sticker and placing it slightly offset from where it was originally stuck. The outline of the original sticker is still there, and nothing else moves.

### When to use relative positioning

Relative positioning is useful when you want to:

- Nudge an element without affecting the layout around it.
- Create a **positioned ancestor** for absolutely positioned children (this is the most common use, and we will see it in the next section).

---

## Absolute Positioning: Relative to a Positioned Ancestor

`position: absolute` removes the element from the normal flow and positions it relative to a reference container. That reference container is the **nearest positioned ancestor**.

### What is a "positioned ancestor"?

In HTML, elements are nested. An **ancestor** is any element that contains the current element. A **descendant** is any element contained inside it.

```html
<div class="outer">            <!-- ancestor -->
  <div class="middle">         <!-- ancestor of .inner, descendant of .outer -->
    <div class="inner"></div>  <!-- descendant -->
  </div>
</div>
```

A **positioned ancestor** is an ancestor whose `position` value is **not** `static`. In this guide, that means it has `position: relative`, `position: absolute`, or `position: fixed`. (Strictly speaking, `position: sticky` also counts, but we are not covering it here.) The browser walks up the ancestor chain from the absolutely positioned element and uses the first positioned ancestor it finds as the reference container.

### The two rules of absolute positioning

1. **If the element has a positioned ancestor**, it is positioned relative to that ancestor's **padding box** — the area inside the ancestor's border, including its padding.
2. **If it has no positioned ancestor**, it is positioned relative to the **initial containing block**, which effectively means the top-left corner of the page.

### Example

```html
<div class="parent">
  <div class="child"></div>
</div>
```

```css
.parent {
  position: relative;  /* now this is a positioned ancestor */
  width: 500px;
  height: 300px;
  background: steelblue;
}

.child {
  position: absolute;
  top: 50px;
  left: 50px;
  width: 100px;
  height: 100px;
  background: tomato;
}
```

Because `.parent` has `position: relative`, the `.child` is positioned 50px from the top and 50px from the left **of the parent**, not of the page.

If you remove `position: relative` from `.parent`, the child will jump to 50px from the top-left corner of the page. This is because, without a positioned ancestor, the child's reference container becomes the initial containing block.

### Absolute elements are removed from the flow

An absolutely positioned element no longer takes up space in the normal layout. Other elements behave as if it is not there. This is useful for overlays, badges, and decorative elements, but it also means you cannot rely on it to push other content around.

### The classic pattern

The most common way to use absolute positioning is:

```css
.parent {
  position: relative;
}

.child {
  position: absolute;
  top: 10px;
  right: 10px;
}
```

The parent acts as the coordinate system; the child is placed inside it. This pattern is used everywhere: close buttons on cards, notification badges, tooltips, and so on. Once you recognize this pattern, you will start seeing it in almost every interface you inspect.

---

## Fixed Positioning: Pinned to the Viewport

`position: fixed` also removes the element from the normal flow, but it positions the element relative to the **browser viewport** — the visible area of the browser window.

```css
.navbar {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
}
```

The key difference from `absolute` is what happens when you scroll:

- An **absolutely positioned** element is tied to the page. If you scroll down, it scrolls with the page.
- A **fixed positioned** element is tied to the viewport. If you scroll, it stays in the same place on the screen.

This makes `fixed` ideal for elements that should always remain visible, such as a sticky header, a floating action button, or a cookie banner.

> **Note:** `fixed` positioning can be affected by certain ancestor properties (such as `transform`, `filter`, `perspective`, `will-change: transform`, or `contain`) that create a new containing block. For beginners, it is enough to know that in ordinary circumstances, `fixed` is relative to the viewport.

### Fixed vs. absolute: a quick comparison

| Behavior | `absolute` | `fixed` |
|---|---|---|
| Reference container | Nearest positioned ancestor (or page) | Viewport |
| Scrolls with the page? | Usually yes (depends on the containing block) | No |
| Removed from normal flow? | Yes | Yes |
| Typical use case | Badges, tooltips, overlays | Sticky headers, floating buttons |

---

## The Offset Properties: `top`, `right`, `bottom`, `left`

The `position` property decides *what* the element is positioned relative to. The offset properties decide *how far* and *in which direction*.

```css
.box {
  position: relative;
  top: 20px;
  left: 50px;
}
```

Each offset property sets the distance between one edge of the element and the corresponding edge of its reference container. (For simplicity, this description refers to the element's border edge. When margins are present, the offset is measured to the element's **margin edge** instead, so margins affect the final position of the border box — see [How Positioning Interacts with the Box Model](#how-positioning-interacts-with-the-box-model).)

- `top` sets the distance from the **top edge** of the reference container to the **top edge** of the element (more precisely, to the element's top margin edge when margins are present).
- `right` sets the distance from the **right edge** of the reference container to the **right edge** of the element (more precisely, to the element's right margin edge).
- `bottom` sets the distance from the **bottom edge** of the reference container to the **bottom edge** of the element (more precisely, to the element's bottom margin edge).
- `left` sets the distance from the **left edge** of the reference container to the **left edge** of the element (more precisely, to the element's left margin edge).

### The meaning changes with the positioning type

The same offset property can behave slightly differently depending on the `position` value:

- With **`relative`**, the reference edges are the element's own original edges in normal flow. So `top: 50px` means "shift the element 50px down from where it would have been."
- With **`absolute`** or **`fixed`**, the reference edges are the edges of the containing block (the positioned ancestor or the viewport). So `top: 50px` means "place the element's top edge 50px below the top edge of the containing block."

This distinction is subtle but important. It explains why the same CSS declaration can produce different visual results depending on the positioning context.

### They only work on positioned elements

As mentioned earlier, these properties have no effect on `position: static` elements. In this guide, they work with `relative`, `absolute`, and `fixed`. (They also work with `sticky`, which is outside the scope of this guide.)

### You do not need all four

Usually you set only the ones you need. For example:

```css
.badge {
  position: absolute;
  top: 8px;
  right: 8px;
}
```

This pins the badge to the top-right corner of its positioned ancestor. You do not need to set `bottom` or `left`.

### What do percentages mean?

Percentage values for `top` and `bottom` are calculated relative to the **height** of the containing block. Percentages for `left` and `right` are calculated relative to the **width** of the containing block. This is true even for `position: relative`: the percentages refer to the containing block's dimensions, not to the element's own size.

```css
.child {
  position: absolute;
  top: 50%;
  left: 50%;
}
```

This places the **top-left corner** of the child at the center of the parent. It does **not** center the child itself. If you want the child to be truly centered, you need to offset it by half of its own width and height (for example, using `transform: translate(-50%, -50%)`, which is outside the scope of this guide).

This is an important distinction: offset percentages refer to the containing block, not to the element being positioned. Keeping this in mind will help you predict where an element will actually appear.

---

## How Positioning Interacts with the Box Model

Recall the CSS box model: content, padding, border, and margin. The `position` offset is applied **after** the normal layout and box model have been calculated — it is an additional shift on top of the element's laid-out position, not a replacement for margin or padding. In other words, offsets and margins stack: the offset moves the element from wherever the box model already placed it.

Consider this:

```css
.box {
  position: relative;
  top: 50px;
  margin-top: 20px;
}
```

The element's normal position already includes the `20px` top margin. Then the `top: 50px` shifts it an additional 50px down. The two effects stack.

In practice:

- **Margin** affects where an in-flow element sits in the normal flow. On positioned elements, margins still affect the final position within the containing block, but they do not reinsert the element into normal flow.
- **Offset properties** shift the element relative to its reference point after layout.

You can use both together. For example, an absolutely positioned element can have margins, and those margins will be added to its offset position.

Understanding this separation helps you debug layout issues: if an element is not where you expect, check both its margins and its positioning offsets.

---

## Stacking and `z-index`

When elements are positioned, they can overlap. Which one appears on top?

The `z-index` property controls the stacking order along the **Z-axis** — the imaginary axis pointing out of the screen toward you. Think of the page as having layers: elements with a higher stacking level appear in front of elements with a lower level.

```css
.box-a {
  position: absolute;
  z-index: 100;
}

.box-b {
  position: absolute;
  z-index: 50;
}
```

Here, `.box-a` will appear on top of `.box-b` because `100` is greater than `50`.

### Default stacking

For elements that participate in stacking, the default level can be thought of as `0`. If you do not set `z-index`, positioned elements are stacked in the order they appear in the HTML, with later elements on top.

> **Technical note:** The actual default value of `z-index` is `auto`, not `0`. For positioned elements, `auto` behaves as if the element has a stacking level of `0` in its stacking context. For beginner purposes, treating the default as `0` is a useful simplification.

### Negative `z-index`

Within the same stacking context, an absolutely positioned element can be pushed behind other content with a negative `z-index`:

```css
.background-decoration {
  position: absolute;
  z-index: -1;
}
```

However, remember that absolutely positioned elements are removed from the normal flow. Changing `z-index` (even to `0`) does not put them back into the flow. If you need an element to participate in normal layout again, use `static` or `relative` positioning. Also note that a negative `z-index` only moves the element behind content **within the same stacking context**; it cannot go behind the background of an ancestor that establishes its own stacking context.

### When does `z-index` apply?

`z-index` only works on positioned elements (`relative`, `absolute`, `fixed`, and also `sticky`, which is outside the scope of this guide). It also works on flex and grid items, but that is beyond the scope of this guide. On a normal `static` element that is not a flex or grid item, `z-index` has no effect.

This is another reason why understanding `position` is essential: it unlocks not just placement control, but also stacking control.

---

## Hands-On: Drawing a Circle and Placing It Inside a Rectangle

Let us put everything together with a small project. The goal is to create a blue rectangle with a red circle positioned near its center.

### Step 1: Create a circle with CSS

A circle can be made from a square by giving it a `border-radius` of `50%`.

```css
.red-circle {
  width: 200px;
  height: 200px;
  background-color: red;
  border-radius: 50%;
}
```

- `width` and `height` make the element a square.
- `background-color` makes it visible.
- `border-radius: 50%` rounds the corners so much that the square becomes a perfect circle.

### Step 2: Build the rectangle

```css
.blue-box {
  width: 500px;
  height: 300px;
  background-color: blue;
}
```

### Step 3: Position the rectangle

We want to shift the rectangle `200px` down and `200px` to the right from where it would normally sit.

```css
.blue-box {
  width: 500px;
  height: 300px;
  background-color: blue;
  position: relative;
  top: 200px;
  left: 200px;
}
```

We use `relative` here because the rectangle has no positioned ancestor and, with no preceding content, its original position is already near the top-left of the page. The `top` and `left` offsets are measured from the rectangle's **own original position**, so a `200px` shift down and right achieves the desired placement. Using `relative` also turns the rectangle into a **positioned ancestor**, which we will need in the next step.

### Step 4: Position the circle relative to the rectangle

Now we want the circle to be centered inside the rectangle. Since the rectangle is `500px` wide and `300px` tall, and the circle is `200px` by `200px`, we offset the circle by `150px` from the left and `50px` from the top.

Because the rectangle is now a positioned ancestor (`position: relative`), we can use `absolute` on the circle:

```css
.red-circle {
  width: 200px;
  height: 200px;
  background-color: red;
  border-radius: 50%;
  position: absolute;
  top: 50px;
  left: 150px;
}
```

The circle is positioned relative to the blue rectangle, not the page. This is the classic parent-child positioning pattern in action.

### Step 5: The complete HTML and CSS

```html
<div class="blue-box">
  <div class="red-circle"></div>
</div>
```

```css
.blue-box {
  width: 500px;
  height: 300px;
  background-color: blue;
  position: relative;
  top: 200px;
  left: 200px;
}

.red-circle {
  width: 200px;
  height: 200px;
  background-color: red;
  border-radius: 50%;
  position: absolute;
  top: 50px;
  left: 150px;
}
```

### Why these numbers?

- `150px` is half of the rectangle's width (`500px`) minus half of the circle's width (`200px / 2 = 100px`). That is, `(500 - 200) / 2 = 150`.
- `50px` is half of the rectangle's height (`300px`) minus half of the circle's height (`200px / 2 = 100px`). That is, `(300 - 200) / 2 = 50`.

This places the circle exactly at the center of the rectangle. Notice that `top` and `left` position the element's **top-left corner**, not its center — the subtraction of half the circle's own size is what makes the centering work.

### Alternative solutions

This challenge can be solved in several ways:

- The rectangle could use `position: absolute` instead of `relative`, because it has no positioned ancestor and would still be positioned relative to the initial containing block (effectively the page).
- The rectangle could use `position: fixed`, because it would then be positioned relative to the viewport. In this simple page with no scrolling and no offsets on `body`, the visual result would be approximately the same — but the two are not truly equivalent, since `fixed` ignores page scroll and is measured from the viewport, not from the element's original position.

Different approaches can produce the same visual result. The important thing is to understand **what each value is relative to**, so that when your layout becomes more complex, you can predict and debug the behavior.

---

## Summary

| Concept | Key takeaway |
|---|---|
| `static` | Default. Offset properties are ignored. |
| `relative` | Shifts from the element's own original position. Original space is preserved. |
| `absolute` | Positioned relative to the nearest positioned ancestor. Removed from normal flow. |
| `fixed` | Positioned relative to the viewport. Removed from normal flow. Stays put when scrolling. |
| Offset properties | Only work on positioned elements. Percentages are relative to the containing block's dimensions. |
| `z-index` | Controls stacking order. Only works on positioned elements (and flex/grid items). |

The core skill in CSS positioning is asking one question: **"What is this element positioned relative to?"** Once you can answer that for any element, the rest of the behavior follows predictably.
