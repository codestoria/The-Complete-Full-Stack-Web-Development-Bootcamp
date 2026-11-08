# Introduction to CSS: Why We Need It and How to Use It

## Contents

1. [The Problem: HTML Describes Structure, Not Appearance](#1-the-problem-html-describes-structure-not-appearance)
2. [What CSS Is](#2-what-css-is)
3. [Why Separating Content and Style Matters](#3-why-separating-content-and-style-matters)
4. [The Anatomy of a CSS Rule](#4-the-anatomy-of-a-css-rule)
5. [Three Ways to Add CSS to HTML](#5-three-ways-to-add-css-to-html)
6. [CSS Selectors: Choosing What to Style](#6-css-selectors-choosing-what-to-style)
7. [Cascade and Specificity: How Conflicts Are Resolved](#7-cascade-and-specificity-how-conflicts-are-resolved)

---

## 1. The Problem: HTML Describes Structure, Not Appearance

**HTML** stands for **HyperText Markup Language**. It is the language used to describe the **structure and meaning** of web content: headings, paragraphs, lists, links, images, and so on. HTML answers one question:

> What is this content?

It does not answer another:

> How should this content look?

An `<h1>` element, for example, tells the browser, "This is the most important heading on the page." It says nothing about which font to use, what color the text should be, or how much space should surround it. Browsers do apply default styles, but those defaults are not a design system.

As the web grew, developers wanted more control over appearance — fonts, colors, spacing, layouts. HTML was not designed for that purpose. Early attempts to solve the problem introduced presentational elements and attributes. **HTML 3.2**, published as a W3C Recommendation in January 1997, standardized presentational elements such as:

- `<font>` — for changing font size, color, and face
- `<center>` — for centering content

These elements did change appearance, but they created a new problem: **HTML became cluttered**. Styling decisions often had to be written directly into the markup. A simple page could quickly become long and difficult to read, and it became hard to distinguish content from presentation. Worse, the presentational elements mentioned here — and many presentational attributes — are now **obsolete** (outdated and no longer recommended).

CSS was created to solve this problem.

---

## 2. What CSS Is

**CSS** stands for **Cascading Style Sheets**. Each part of the name explains something important about how CSS works.

### 2.1 "Cascading"

The word "cascading" is often illustrated with a waterfall, where rules flow through a series of levels. In CSS, however, the key idea is conflict resolution: when multiple style rules apply to the same element, the browser must decide which rule wins. This decision-making process is called **the cascade**.

The cascade considers several factors, including where the rule came from, how important it is, how specific its selector is, and the order in which rules appear. You do not need to master all of these details yet. For now, remember the core idea:

> When several CSS rules could apply to the same element, the browser resolves the conflict using the cascade.

Later sections introduce specificity, one of the most important parts of the cascade.

### 2.2 "Style Sheet"

A **style sheet** is a collection of rules that describe how content should look. Just as HTML is a **markup language**, CSS is a **style sheet language**. It describes presentation: colors, fonts, spacing, layout, borders, and much more.

CSS is the style sheet language that browsers understand directly. There are also tools such as **Sass** and **Less**, but these are best understood as **CSS preprocessors**: developers write in them, and the code is compiled into ordinary CSS that browsers can read. They are useful tools, but the foundation is still CSS itself.

In short:

- **HTML** provides structure and meaning.
- **CSS** provides presentation and appearance.

---

## 3. Why Separating Content and Style Matters

In 1996, the W3C published CSS1 as a W3C Recommendation; it was edited by **Håkon Wium Lie** and **Bert Bos**. Lie is often called the "father of CSS." The key idea was revolutionary:

> **Separate styling from content.**

Before CSS, changing how a website looked usually meant modifying the HTML itself. This caused several problems:

- The HTML file became cluttered with styling instructions.
- Changing the look of a multi-page website required editing every single page.
- Content and presentation were tangled together, making both harder to maintain.

CSS solved this by introducing a **separation of concerns**. In manufacturing, one team might make headlights while another makes wheels, rather than having one person build an entire car. CSS applies the same principle to the web:

- **HTML** focuses on content and structure.
- **CSS** focuses on presentation and appearance.

This modularity is a core principle in programming. It makes websites easier to build, maintain, and change.

---

## 4. The Anatomy of a CSS Rule

Before learning where to put CSS, you need to recognize the basic structure of a CSS rule:

```css
selector {
  property: value;
}
```

A CSS rule has two main parts:

- The **selector** outside the curly braces determines **which HTML elements** the rule applies to.
- The **declaration block** inside the curly braces contains zero or more **declarations**; in practice, a useful rule usually contains at least one.
  A declaration is written as `property: value;`.

For example:

```css
h1 {
  color: red;
  font-size: 32px;
}
```

Here:

- `h1` is the selector.
- `color: red;` and `font-size: 32px;` are declarations.
- `color` and `font-size` are properties.
- `red` and `32px` are values.

Declarations are usually separated by semicolons. The final semicolon in a declaration block is technically optional, but including it is a common convention and makes later edits safer.

CSS comments are written between `/*` and `*/`:

```css
/* This is a CSS comment. Browsers ignore it. */
```

---

## 5. Three Ways to Add CSS to HTML

There are three common ways to attach CSS to an HTML document:

1. **Inline CSS**
2. **Internal CSS**
3. **External CSS**

Each has its own use case, advantages, and drawbacks. Understanding the trade-offs will help you choose the right approach for the job.

### 5.1 Inline CSS

**Inline CSS** is written directly inside an HTML element's opening tag using the `style` attribute.

```html
<h1 style="color: blue;">Hello World</h1>
```

**How it works:**

- The `style` attribute is a global HTML attribute, so it can be specified on all HTML elements, though it may have no visible effect on some.
- Inside the attribute value, you write CSS declarations in the format `property: value;`.
- In the example above, `color` is the property and `blue` is the value.

**When to use it:**

- When you want to style a **single, specific element**.
- For quick testing or one-off changes.

**Drawbacks:**

- If many elements should share the same style, you must repeat the inline style on each one. This is tedious and error-prone.
- It mixes content and presentation, which is exactly what CSS was designed to avoid.
- Among normal author declarations, inline styles take precedence over selector-based rules, so they can be difficult to override later.
- A normal inline style can still be overridden by an author `!important` declaration in a selector-based rule, or by a user `!important` declaration, which outranks author `!important` declarations. An inline style that itself uses `!important` is an author `!important` declaration: it outranks normal inline styles and normal selector-based rules, but a user `!important` declaration can still override it.
- It is not recommended for styling an entire document.

### 5.2 Internal CSS

**Internal CSS** is written inside a `<style>` element, usually placed in the `<head>` section of the HTML document.

```html
<head>
  <style>
    h1 {
      color: red;
    }
  </style>
</head>
```

**How it works:**

- The `<style>` element has an opening and closing tag.
- Inside it, you write CSS rules.
- Unlike inline CSS, ordinary internal CSS rules require a **selector** before the declaration block. The selector tells the browser which elements the rule applies to.
- In the example above, `h1` selects all `<h1>` elements on the page.

**When to use it:**

- When you want to style a **single HTML document**.
- When the styles apply only to the page where the `<style>` element lives.

**Drawbacks:**

- It is not suitable for multi-page websites, because you would need to duplicate the `<style>` block on every page.
- It still keeps CSS inside the HTML file, so it does not fully separate content from presentation.

#### Where Should the `<style>` Element Go?

By convention, a `<style>` element is placed inside the `<head>`:

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      h1 {
        color: red;
      }
    </style>
  </head>
  <body>
    <h1>Hello World</h1>
  </body>
</html>
```

Placing `<style>` in the `<head>` is the recommended default for several reasons:

- The browser can usually discover and apply the styles before the first paint of the page body.
- It helps avoid a brief moment when the page appears unstyled before the CSS is applied. This is often called **flash of unstyled content**, or **FOUC**.
- It keeps document-level metadata and styling information together in the `<head>`.
- It follows common HTML structure and validation conventions.

However, this is a convention and best practice, not a technical requirement for CSS to work. Browsers generally still apply a `<style>` element placed elsewhere, such as in the `<body>`:

```html
<body>
  <h1>Hello World</h1>

  <style>
    h1 {
      color: red;
    }
  </style>
</body>
```

The CSS can still match elements. However, placing `<style>` after content may cause the browser to render the page first and restyle it later, producing a visible flash or layout shift; it also makes the document structure less clean. More importantly, according to the HTML specification, `<style>` is metadata content and is allowed only in the `<head>`; placing it in the `<body>` is non-conforming, and validators will typically report it as an error.

The practical rule is:

> Put document-level `<style>` blocks in the `<head>` by default. Browsers may still apply them elsewhere, but other placements are non-conforming and can cause avoidable rendering and maintenance problems.

For a single page, internal CSS in the `<head>` is fine. For a multi-page site, external CSS is usually better. Inline CSS should be reserved for one-off cases.

### 5.3 External CSS

**External CSS** lives in a separate file with a `.css` extension, commonly named `styles.css` or `main.css`.

```css
/* styles.css */
h1 {
  color: green;
}
```

To connect the stylesheet to your HTML, add a `<link>` element inside the `<head>`:

```html
<head>
  <link rel="stylesheet" href="styles.css">
</head>
```

**How it works:**

- The `<link>` element is a void element in HTML; it does not have a closing tag.
- `rel="stylesheet"` tells the browser the role of the linked file: it is a stylesheet.
- `href="styles.css"` tells the browser where to find the file. In this case, the file is in the same directory as the HTML page.

**When to use it:**

- **Most of the time**, especially for multi-page websites.
- One CSS file can style an entire website with many pages.

**Advantages:**

- Clean separation of content in HTML and presentation in CSS.
- Easy to maintain: change one CSS file, and every linked page updates.
- The same stylesheet can be reused across many pages.
- Browsers can cache external CSS files, which can improve performance on later page loads.
- This is the standard approach in professional web development.

### 5.4 Quick Comparison

| Method | Location | Scope | Best For | Main Trade-off |
|--------|----------|-------|----------|----------------|
| Inline | Inside an element's `style` attribute | Single element | One-off styling | Repetitive and mixes content with presentation |
| Internal | Inside a `<style>` element, usually in `<head>` | Single page | Single-page sites | Must be duplicated across pages |
| External | Separate `.css` file linked via `<link>` | Entire website | Multi-page sites and most real projects | Requires managing an additional file |

---

## 6. CSS Selectors: Choosing What to Style

A **selector** determines which HTML elements a CSS rule applies to. This section introduces the most common selector types, starting with simple/common selectors and then covering more specific ones. The universal selector is included last as a special broad selector.

### 6.1 Element Selector

The **element selector** is the simplest selector. It targets all elements of a given HTML tag.

```css
h2 {
  color: red;
}
```

This selects **all** `<h2>` elements on the page and turns their text red. If you have three `<h2>` headings, all three will be affected.

**Use when:** You want to style every instance of a particular tag.

### 6.2 Class Selector

A **class** is an attribute you can add to any HTML element to group elements together. In CSS, you select a class using a **dot (`.`)** followed immediately by the class name. Do not put a space between the dot and the class name.

```html
<h2 class="red-text">Heading</h2>
<p class="red-text">This paragraph shares the same class.</p>
```

```css
.red-text {
  color: red;
}
```

**Key points:**

- In standards mode, class name matching in CSS class selectors is **case-sensitive**: `.red-text` is not the same as `.Red-Text`.
- Multiple elements can share the same class, even if they are different types of elements, such as an `<h2>` and a `<p>`.
- This makes the class selector **versatile and reusable**.

**Use when:** You want to apply the same style to a group of elements, regardless of their tag type.

### 6.3 ID Selector

An **ID** is an attribute that must be **unique** within a single HTML document. In CSS, you select an ID using a **hash symbol (`#`)** followed immediately by the ID name.

```html
<p id="main">This is the main paragraph.</p>
```

```css
#main {
  color: blue;
}
```

**Key points:**

- An ID value must be **unique** within a single HTML document: the same `id` value must not appear more than once.
- Uniqueness and specificity are separate concerns: in the cascade, ID selectors have higher specificity than class selectors.
- Use IDs sparingly. They are meant for unique elements, not for general reusable styling.

**Use when:** You need to target one specific, unique element on a page.

### 6.4 Attribute Selector

The **attribute selector** targets elements based on their attributes or attribute values. The syntax uses **square brackets `[ ]`**.

```css
/* Select all <p> elements that have a draggable attribute, regardless of its value */
p[draggable] {
  color: red;
}

/* Select all <p> elements where draggable="false" */
p[draggable="false"] {
  color: blue;
}
```

**Key points:**

- The element name before the brackets is optional, but including it often makes the rule more precise and easier to understand.
- You can select by attribute presence alone, such as `[draggable]`, or by a specific value, such as `[draggable="false"]`.
- Attribute selectors are powerful and flexible.

**Use when:** You want to style elements based on their attributes or attribute values.

### 6.5 Universal Selector

The **universal selector** is simply an **asterisk (`*`)**.

```css
* {
  text-align: center;
}
```

This selects **every element** on the page (but not pseudo-elements) and applies the style to all of them. It is the broadest selector possible.

**Use when:** You want to apply a style globally, such as resetting margins or setting a default box model. Use it carefully, because it can affect far more elements than you may expect.

### 6.6 Selector Summary

| Selector | Syntax | Example | Selects |
|----------|--------|---------|---------|
| Element | `tag` | `h1 { }` | All `<h1>` elements |
| Class | `.classname` | `.red-text { }` | All elements with `class="red-text"` |
| ID | `#idname` | `#main { }` | The one element with `id="main"` |
| Attribute | `tag[attr]` or `tag[attr="value"]` | `p[draggable="false"] { }` | Elements matching the attribute condition |
| Universal | `*` | `* { }` | Every element |

---

## 7. Cascade and Specificity: How Conflicts Are Resolved

A single element can be targeted by multiple CSS rules. For example, an element might match an element selector, a class selector, and an ID selector at the same time. If those rules set the same property, the browser must decide which rule wins. The overall decision-making process is the **cascade**. **Specificity** is one part of the cascade.

In a simplified model that considers only normal author declarations (ignoring `!important`, user styles, and browser defaults), the browser resolves conflicts using the following simplified priority order, from highest to lowest:

1. **Declarations in the `style` attribute** (inline styles)
2. **ID selectors**
3. **Class selectors** and **attribute selectors**
4. **Element selectors**
5. **Universal selector**

> This list is a teaching simplification. Strictly speaking, declarations in the `style` attribute are not selectors and are not compared by specificity; in the cascade they are handled as a separate step that outranks normal selector-based author rules.

When two rules have the same origin, importance, and specificity, the rule that appears later in the CSS wins. Consider this example:

```css
p {
  color: black;
}

.note {
  color: blue;
}

#intro {
  color: red;
}
```

If the HTML is:

```html
<p id="intro" class="note">Hello</p>
```

the text will be red because the ID selector is more specific than the class selector and the element selector. If the ID were removed, the class rule would win. If both the ID and class were removed, the element rule would apply.

An inline style would override these normal selector-based rules because, among normal author declarations, declarations in the `style` attribute take precedence over selector-based rules in the cascade. This priority is not absolute, however: author `!important` declarations, for example, can override normal inline styles. This is one reason inline CSS should be used sparingly.

Specificity only matters when rules conflict on the same element and the same property. The full cascade also considers rule origin and importance, which are more advanced topics. For now, remember these key ideas:

- More specific selectors generally override less specific ones.
- If specificity is equal, later rules win.
- Among normal author declarations, declarations in the `style` attribute take precedence over selector-based rules in the cascade, so they can be difficult to override later.
- Use the simplest selector that clearly expresses your intent.
