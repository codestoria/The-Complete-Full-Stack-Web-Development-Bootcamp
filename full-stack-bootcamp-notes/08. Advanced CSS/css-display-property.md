# CSS `display` Property: Controlling Page Layout

## Contents

1. [Why the `display` Property Matters](#1-why-the-display-property-matters)
2. [Default Layout Behavior: Block and Inline](#2-default-layout-behavior-block-and-inline)
3. [The `display` Property and Its Common Values](#3-the-display-property-and-its-common-values)
4. [Hiding Elements: `display: none`](#4-hiding-elements-display-none)
5. [Comparison Table](#5-comparison-table)
6. [Hands-On Practice: The Three-Square Challenge](#6-hands-on-practice-the-three-square-challenge)
7. [Key Takeaways](#7-key-takeaways)

---

## 1. Why the `display` Property Matters

When a browser renders an HTML page, every element follows a default layout behavior. These defaults come from the browser's built-in stylesheet, often called the **user-agent stylesheet**. For example, a `<p>` element is treated as a **block-level** element by default. A block-level element starts on a new line and, when its `width` is `auto`, usually stretches to fill the available width of its container.

This is why a short paragraph can still stretch across most of the page:

```text
[                Paragraph text                ]
[                Next element                  ]
```

The paragraph may contain only a few words, but its box still occupies the available horizontal space, and the next element begins on a new line.

But what if you want a short paragraph to be only as wide as its text, so that another element can sit beside it? Or what if you want two headings to appear on the same line? To control this behavior, you use the CSS `display` property.

The `display` property tells the browser **how an element should participate in page layout**:

- Should the element start on a new line?
- Should it sit beside other elements?
- Should its width and height be controllable?
- Should it be removed from the layout entirely?

To understand these choices, it helps to know one key term: **normal flow**. Normal flow is the browser's default layout algorithm. In normal flow, block-level boxes are usually stacked vertically, one after another, while inline-level boxes are laid out horizontally within lines of text.

Mastering `display` is one of the first major steps toward controlling website layout.

> **Scope note:** This tutorial focuses on the core `display` values `block`, `inline`, `inline-block`, and `none` (`none` removes the element from layout entirely and generates no box). Modern CSS also provides layout modes such as `flex` and `grid`, but those are separate topics.

---

## 2. Default Layout Behavior: Block and Inline

Most HTML elements follow one of two basic layout behaviors by default: **block** or **inline**.

| Behavior | Description | Common Examples |
|---|---|---|
| **Block** | Starts on a new line. By default, a block-level element with `width: auto` fills the available width of its container. The next block-level element starts on a new line. | `<p>`, `<h1>`–`<h6>`, `<div>` |
| **Inline** | Flows within text and takes up only as much width as its content needs. It can share a line with other inline content. | `<span>`, `<a>`, `<strong>` |

### Example: A `<span>` Inside a Paragraph

Consider this HTML:

```html
<p>Hello, <span>Beautiful</span> World</p>
```

The `<span>` element wraps the word “Beautiful” inside the paragraph. By default, `<span>` is an **inline** element, so it sits naturally within the flow of the paragraph text without breaking onto a new line.

```text
Hello, Beautiful World
```

This is why `<span>` is often used to style a small piece of text inside another element: it does not disturb the surrounding layout.

---

## 3. The `display` Property and Its Common Values

The `display` property accepts many values. For basic layout, three values are especially important:

```css
/* Choose one value per element: */
.block        { display: block; }        /* Starts on a new line; width and height can be set */
.inline       { display: inline; }       /* Flows with text; width and height do not apply to ordinary inline elements */
.inline-block { display: inline-block; } /* Sits in line, but width and height can be set */
```

Changing `display` changes how an element is laid out. It does **not** change the element's meaning or semantic role. For example, a `<p>` with `display: inline` is still a paragraph semantically; only its layout behavior changes. Note also that `display: none` generally removes the element from the accessibility tree.

Let’s examine each value in detail.

### 3.1 `display: block`

When an element has `display: block`:

- It starts on a **new line**.
- By default, when its `width` is `auto`, it fills the available width of its container.
- In normal flow, the next block-level element starts on a new line below it.
- You **can** set its `width` and `height`.

**Behavior illustrated:**

```text
[        Block Element (fills available width)        ]
[        Next Element (new line)                       ]
```

Even if you reduce the width of a block-level element, the next block-level element will **not** move up beside it in normal flow. The block-level element still starts on its own line, so the following block-level element begins below it. The remaining horizontal space is simply left empty unless you use another layout method such as floats, `flex`, or `grid`. Positioning is a separate layout mechanism and is not part of normal flow.

**Example:**

```css
h2 {
  display: block;
}
```

If you have two `<h2>` elements with `display: block`, they will stack vertically:

```text
[Heading 1]
[Heading 2]
```

### 3.2 `display: inline`

When an element has `display: inline`:

- It takes up only as much width as its content requires.
- It can **share a horizontal line** with other inline content.
- You **cannot** set its `width` or `height`; the browser ignores those properties for ordinary inline elements.

**Behavior illustrated:**

```text
[Inline 1][Inline 2][Inline 3]  ← sit side by side
```

**Example:**

```css
h2 {
  display: inline;
}
```

With `display: inline`, two `<h2>` elements can appear next to each other on the same line, as long as there is enough space.

**Why can’t you set width and height?**

Ordinary inline elements participate in line layout. They generate inline boxes that are laid out within lines of text, and their size is determined by their content and the surrounding line layout rather than by explicit `width` or `height` values. For example, if the content is the word “Hello,” the inline box fragment is only as wide as “Hello.” If the content is a longer sentence, the inline element can wrap across multiple lines. Because the browser lays these boxes out as part of line layout, explicit `width` and `height` values do not apply to ordinary inline elements.

> **Note:** Replaced inline elements such as `<img>` are a special case because they have intrinsic dimensions. For ordinary inline elements such as `<span>` and `<a>`, `width` and `height` do not apply.

### 3.3 `display: inline-block`

`display: inline-block` is a **hybrid** of block and inline behavior.

- Like `inline`, elements can sit **side by side** on the same line.
- Like `block`, you **can** set `width` and `height`.

**Behavior illustrated:**

```text
[Inline-Block 200×200][Inline-Block 20×100]
```

**Example:**

```css
.box {
  display: inline-block;
  width: 200px;
  height: 200px;
}
```

If you have two `inline-block` elements—for example, one that is 200×200 and another that is 20×100—they will:

- Respect their custom widths and heights.
- Appear on the same horizontal line **if there is enough space**.
- Wrap to the next line if the container is too narrow.

This makes `inline-block` useful for creating simple rows of cards, buttons, or image thumbnails.

> **Practical note:** Because `inline-block` elements are still inline-level, whitespace in the HTML between them can create small gaps. For example, a newline between two `inline-block` elements may appear as a space in the rendered layout. This is one reason modern layouts often use `flex` or `grid` instead.

---

## 4. Hiding Elements: `display: none`

There is one more common value worth mentioning:

```css
.hidden {
  display: none;
}
```

`display: none` **removes the element from the page layout entirely**. The element generates no box at all, takes up no space, and is not rendered. This is useful when you want to hide something after a button click or when a to-do list item is completed.

> **Important distinction:** `display: none` is different from `visibility: hidden`.
>
> - `display: none` removes the element from layout and generally removes it from the accessibility tree. It takes up no space.
> - `visibility: hidden` makes the element invisible, but it still takes up space in the layout. A descendant can be made visible again with `visibility: visible`.

---

## 5. Comparison Table

| `display` value | Starts on a new line? | Can sit beside other elements? | Can set `width` / `height`? | Space in layout | Default width behavior |
|---|---|---|---|---|---|
| `block` | Yes, in normal flow | No, in normal flow | Yes | Occupies a block-level box in normal flow | Fills available width when `width: auto` |
| `inline` | No | Yes | No, for ordinary inline elements | Takes up space in lines of text | Fits content |
| `inline-block` | No | Yes | Yes | Takes up space in lines of text | Fits content unless a width is set |
| `none` | Removed from layout | Removed from layout | Not applicable | Takes up no space | No box is generated |

---

## 6. Hands-On Practice: The Three-Square Challenge

Let’s apply what we have learned with a real exercise.

### Setup

You have three paragraph elements (`<p>`), each styled as a 200 px × 200 px square with a different color. Initially, they use the default `display: block`.

```html
<div class="container">
  <p class="square red">Red</p>
  <p class="square green">Green</p>
  <p class="square blue">Blue</p>
</div>
```

```css
.square {
  width: 200px;
  height: 200px;
  margin: 0;
  color: white;
  text-align: center;
  line-height: 200px;
}

.red {
  background-color: #d33;
}

.green {
  background-color: #3a3;
}

.blue {
  background-color: #33d;
}
```

Because `<p>` elements are block-level by default, the squares stack vertically:

```text
[Red Block]
[Green Block]
[Blue Block]
```

### Challenge 1: Line Them Up Horizontally

**Goal:**

```text
[Red][Green][Blue]
```

Small gaps may appear between the squares if the HTML contains whitespace between the elements.

**Solution:**

Change the display of all three elements to `inline-block`.

```css
/* Add `display: inline-block` to the existing .square rule. */
.square {
  display: inline-block;
  width: 200px;
  height: 200px;
  margin: 0;
  color: white;
  text-align: center;
  line-height: 200px;
}
```

**Why this works:** `inline-block` lets the elements keep their 200×200 size while also allowing them to sit next to each other on the same line.

> If the browser window is too narrow to fit all three squares, they will wrap as needed. If the container is wide enough—roughly 600px plus any whitespace gaps—all three squares line up horizontally.
>
> If you see small gaps between the squares, they come from whitespace in the HTML between inline-level boxes. You can remove that whitespace in the HTML, but modern layouts often use `flex` or `grid` to control spacing more reliably.

### Challenge 2: Stack Them Vertically

**Goal:**

```text
[Red]
[Green]
[Blue]
```

**Solution:**

Change the display of all three elements to `block`.

```css
/* Change `display` to `block` in the existing .square rule. */
.square {
  display: block;
  width: 200px;
  height: 200px;
  margin: 0;
  color: white;
  text-align: center;
  line-height: 200px;
}
```

**Why this works:** `block` makes each element start on its own line. Even though each square is only 200 pixels wide, block-level elements do not sit beside each other in normal flow. They stack vertically.

> Notice how the same HTML can produce two very different layouts simply by changing the `display` value. This is the power of CSS layout.

---

## 7. Key Takeaways

1. **`display` controls layout behavior.** It determines whether an element behaves like a block, inline, or inline-block element.
2. **Block-level elements** start on a new line and, by default, fill the available width when `width: auto`. You can set their `width` and `height`.
3. **Inline elements** flow with text and can sit side by side. For ordinary inline elements, `width` and `height` do not apply.
4. **Inline-block elements** combine both behaviors: they can sit side by side, and you can set their `width` and `height`.
5. **`display: none`** completely removes an element from the layout. It takes up no space.
6. **`visibility: hidden`** hides an element but leaves its space in the layout.
7. **You can generally change an element’s `display` value** to suit your layout needs, even if its default value is different. However, changing `display` does not change the element's semantic meaning. (Note: `display: none` removes the element from both layout and the accessibility tree.)
8. **Use the right tool for the job.** `inline-block` is useful for simple inline rows, but modern layouts often use `flex` or `grid` for more complex alignment and spacing.
