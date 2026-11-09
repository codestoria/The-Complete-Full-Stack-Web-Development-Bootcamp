# Understanding the CSS Cascade: How Browsers Decide Which Rule Wins

## Contents

1. [Introduction](#introduction)
2. [The Problem the Cascade Solves](#the-problem-the-cascade-solves)
3. [The Cascade in One Sentence](#the-cascade-in-one-sentence)
4. [The Real Order of Comparison](#the-real-order-of-comparison)
5. [1. Origin and Importance](#1-origin-and-importance)
6. [2. Inline Styles](#2-inline-styles)
7. [3. Specificity](#3-specificity)
8. [4. Source Order](#4-source-order)
9. [Putting It All Together](#putting-it-all-together)
10. [Visual Summary](#visual-summary)
11. [Practice Scenarios](#practice-scenarios)
12. [Practical Exercise](#practical-exercise)
13. [Summary](#summary)

---

## Introduction

When you write CSS, you will often create more than one rule that targets the same HTML element. For example, one rule may set an `h1` **element** to blue, and another rule may later set the same `h1` **element** to green.

Which rule should the browser apply?

The answer is determined by the **CSS Cascade**. The word *cascading* in **Cascading Style Sheets** refers to the browser's rules for resolving conflicts between competing CSS declarations.

This guide explains how the cascade works in a practical, beginner-friendly way. By the end, you should be able to look at several conflicting CSS rules and predict which one will win — and explain why.

> **Goal:** Understand why a style did or did not apply, instead of guessing.

Why does this matter? Because CSS bugs are often not bugs at all — they are the cascade doing exactly what it was designed to do. When you understand the cascade, you can debug style conflicts with confidence instead of adding random `!important` flags and hoping for the best.

---

## The Problem the Cascade Solves

Consider this HTML and CSS:

```css
li {
  color: green;
}
```

```html
<li style="color: red;">Item</li>
```

The browser now has two declarations for the same element and the same property:

- `color: green` from the stylesheet
- `color: red` from the inline style

The browser cannot apply both colors. It needs a consistent, predictable way to choose one. That decision process is the cascade.

To understand the cascade, keep these terms in mind:

| Term | Meaning |
|---|---|
| **Declaration** | A single property-value pair, such as `color: red` |
| **Rule** | A selector plus one or more declarations, such as `h1 { color: red; }` |
| **Selector** | The part that chooses which elements the rule applies to, such as `h1` or `.title` |
| **Conflict** | Two or more declarations try to set the same property on the same element |
| **Winner** | The declaration the browser finally applies |

The cascade only matters when there is a conflict. If only one declaration sets a property, that declaration normally applies. The cascade is the tie-breaking system that kicks in when multiple rules compete.

---

## The Cascade in One Sentence

For ordinary author-written CSS, the browser compares conflicting declarations in this order:

> **Origin and importance first, then inline styles, then specificity, then source order. The first step that produces a clear winner decides the result.**

For author-only CSS, the origin comparison is often tied, so the first practical question is usually whether one declaration is `!important`. **If there is no `!important` difference, the next question is whether one side is an inline style, and only then does specificity come into play.**

This sentence is a simplification for everyday CSS. In particular, “inline styles” is not a separate origin: it is a style-attribute step that is compared after origin and importance. The next sections explain each part carefully.

Think of it as a series of filters. At each step, the browser asks a yes/no question. If one declaration clearly wins, the comparison stops. If the declarations are still tied, the browser moves to the next step.

---

## The Real Order of Comparison

A common beginner mistake is to think that the rule appearing later always wins. That is not true. Source order only matters after more important comparisons have been made.

For typical author CSS, use this decision order:

| Step | What Is Compared | Key Question |
|---:|---|---|
| 1 | **Origin and importance** | Is one declaration `!important`, or do they come from different origins? |
| 2 | **Inline style** | Is one declaration in a `style` attribute? |
| 3 | **Specificity** | Is one selector more specific? |
| 4 | **Source order** | Which rule appears later? |

The first step that separates the declarations determines the winner. Later steps only matter when earlier steps are tied.

This is why you cannot simply say "the last rule wins." A later rule with lower specificity will lose to an earlier rule with higher specificity. The cascade checks importance first, then inline styles, then specificity, and only then source order.

---

## 1. Origin and Importance

### What Is Origin?

**Origin** means where the CSS comes from. In everyday web development, the most important origin is:

- **Author CSS** — CSS that you, the developer, write. This includes external stylesheets, internal `<style>` blocks, and inline styles.

Browsers also have their own default styles, called **user-agent styles**, and users may apply their own custom styles. Those origins matter in advanced cases, but for most author-written CSS, you are comparing one author declaration against another.

Why does origin matter? Because the cascade must balance the interests of the browser, the user, and the developer. A browser's default styles should be easy for authors to override, while user accessibility preferences should be protected. The full origin order reflects these priorities, but for this guide, we focus on author CSS because that is what you write day to day.

### What Is Importance?

A declaration can be marked with the `!important` keyword:

```css
h1 {
  color: purple !important;
}
```

The `!important` flag changes the declaration's priority. Among author-written CSS, an `!important` declaration beats a normal declaration.

Example:

```css
/* External stylesheet */
h1 {
  color: green !important;
}
```

```html
<h1 style="color: red;">Hello</h1>
```

The text is **green**, not red. The inline style is normal, but the stylesheet declaration is `!important`.

Why does this work? Because importance is checked before inline styles. The `!important` flag moves the declaration into a higher-priority category, so the browser never even reaches the inline-style comparison.

### Important Details About `!important`

- It is **not** a specificity boost. It changes the importance category.
- It should be used sparingly. Overusing it makes CSS harder to maintain because it breaks the normal cascade order.
- It is not absolutely the highest priority in all of CSS. For normal declarations, the origin order is user-agent < user < author **(where `<` means lower priority)**; for `!important` declarations, the order reverses: author `!important` < user `!important` < user-agent `!important`. **Transitions and animations have their own defined positions in the cascade; this guide does not cover them.**
- Good uses include overriding third-party styles you cannot edit, temporary debugging, and specific accessibility requirements.

A useful rule of thumb: if you find yourself reaching for `!important` often, the real problem is usually that your selectors are not specific enough or your stylesheet order is wrong. Fix the root cause instead of forcing a win.

---

## 2. Inline Styles

An inline style is written directly on an element with the `style` attribute:

```html
<h1 style="color: red;">Hello</h1>
```

For **normal** declarations, an inline style beats any normal author stylesheet rule, regardless of how specific the stylesheet selector is.

Example:

```html
<h1 id="main-title" style="color: blue;">Hello</h1>
```

```css
#main-title {
  color: green;
}
```

The text is **blue**. The ID selector is very specific, but the inline style is still stronger for normal declarations.

Why is this the case? Because the cascade checks inline styles before specificity. **Within the same origin and importance tier, a `style` attribute is compared at a separate cascade sorting step before selector specificity. This ordering applies to both normal and `!important` declarations.** This makes sense from a design perspective: an inline style is the most direct, local way to style a specific element, so it should beat general stylesheet rules.

However, this does **not** mean inline styles always win. A stylesheet declaration with `!important` beats a normal inline style:

```css
#main-title {
  color: green !important;
}
```

Now the text becomes **green**.

### External vs. Internal Stylesheets

External and internal stylesheets are both author stylesheets. There is no automatic rule that "internal always beats external." If their declarations have the same importance and specificity, the order in the HTML document decides the winner.

Example:

```html
<link rel="stylesheet" href="style.css">
<style>
  h1 {
    color: blue;
  }
</style>
```

If `style.css` also contains `h1 { color: green; }`, then the internal `<style>` rule comes later in the document. With equal importance and specificity, the text is **blue**.

If the `<style>` block appeared before the `<link>`, the external rule would come later and could win instead.

Why does this matter? Because many beginners assume that external files always override internal styles, or vice versa. In reality, both are author styles, and their relative position in the HTML document determines the winner when everything else is equal.

---

## 3. Specificity

**Specificity** measures how precisely a selector targets an element. When two stylesheet declarations have the same origin and importance, the more specific selector wins.

Why do we need specificity? Because it lets you write general rules (like `li { color: green; }`) and then override them with more targeted rules (like `.highlight { color: red; }`) without relying on source order. Specificity gives you a way to express "this rule is more important for this element" directly in the selector.

### The Specificity Tuple

A useful way to think about specificity is as a three-part tuple:

```text
(IDs, classes/attributes/pseudo-classes, elements/pseudo-elements)
```

| Selector Type | Example | Specificity |
|---|---|---:|
| Universal selector | `*` | `(0,0,0)` |
| Element selector | `li` | `(0,0,1)` |
| Pseudo-element | `::before` | `(0,0,1)` |
| Class selector | `.first-class` | `(0,1,0)` |
| Attribute selector | `[draggable]` | `(0,1,0)` |
| Pseudo-class | `:hover` | `(0,1,0)` |
| ID selector | `#first-id` | `(1,0,0)` |

**Note:** This table covers common selectors only. It does not cover special functional pseudo-classes such as `:where()` (zero specificity) and `:is()`/`:not()` (specificity taken from the most specific argument), or cascade layers.

Important corrections:

- **Attribute selectors and class selectors have the same specificity.** Attribute selectors are not automatically more specific than classes.
- **Pseudo-classes such as `:hover` count like classes.**
- **Pseudo-elements such as `::before` count like elements.**
- **Inline styles are special**: they are compared at a separate cascade sorting step before normal selector specificity.

### How to Compare Specificity

Compare the tuple from left to right. Do not treat it as a decimal number.

This is a critical point. Specificity is not like a score where `(0,3,0)` equals `0.3` and `(1,0,0)` equals `1.0`. Instead, compare the first value. If one is larger, that selector wins immediately. Only if the first values are equal do you compare the second values, and so on.

Example:

```css
#main-title {
  color: green;
}

.title.active.featured {
  color: blue;
}
```

- `#main-title` has specificity `(1,0,0)`.
- `.title.active.featured` has specificity `(0,3,0)`.

The ID selector wins because the first value is larger. Three classes do not beat one ID.

Another example:

```css
li.active {
  color: red;
}

li {
  color: blue;
}
```

- `li.active` has specificity `(0,1,1)`.
- `li` has specificity `(0,0,1)`.

`li.active` wins.

### Specificity Example

```html
<li id="first-id" class="first-class" draggable="true">Item</li>
```

```css
li {
  color: green;
}

.first-class {
  color: blue;
}

[draggable] {
  color: purple;
}

#first-id {
  color: orange;
}
```

The ID selector `#first-id` has the highest specificity, so the text is **orange**.

If the ID rule were removed, `.first-class` and `[draggable]` would have equal specificity. In that case, the one that appears later in the source would win. Here, `[draggable]` appears later, so the text would be **purple**.

Why are attribute selectors and classes equal? Because both target elements based on a general characteristic rather than a unique identifier. The cascade treats them as equally precise, which is why source order becomes the tie-breaker.

---

## 4. Source Order

**Source order** means where a rule appears in the CSS source. It is also sometimes called **order of appearance**. Source order is the final tie-breaker.

When two declarations have:

- the same origin and importance,
- the same inline-style status,
- and the same specificity,

then the declaration that appears **later** wins.

Example:

```css
li {
  color: red;
}

li {
  color: blue;
}
```

Both rules target the same element, set the same property, and have the same specificity. The second rule appears later, so the text becomes **blue**.

Why does source order matter? Because it gives you a predictable way to override earlier rules when everything else is equal. It also explains why the order of your stylesheets and rules matters, even when the selectors are identical.

### Important Point

Source order is **not** the first thing the browser checks. It only decides the winner after importance, inline style, and specificity are tied.

A common misconception is:

> "The later rule always wins."

A more accurate statement is:

> "The later rule wins only when all earlier cascade comparisons are equal."

This distinction is crucial. A later rule with lower specificity will lose to an earlier rule with higher specificity. Source order is the last resort, not the first check.

---

## Putting It All Together

When you are unsure which declaration wins, follow these steps:

1. **Find all declarations** that set the same property on the same element.
2. **Compare importance.** Among author CSS, does one declaration use `!important`? An author `!important` declaration beats a normal author declaration.
3. **Compare style-attribute declarations.** After origin and importance are tied, does one declaration come from a `style` attribute? A style-attribute declaration outranks stylesheet declarations in the same origin/importance tier: normal inline beats normal stylesheet rules, and inline `!important` beats stylesheet `!important`.
4. **Compare specificity.** Among remaining stylesheet rules, which selector is more specific?
5. **Compare source order.** If specificity is still tied, the later rule wins.
6. **Apply the winner.** Only the winning declaration's value is used.

This procedure handles most beginner and intermediate CSS conflicts. The key is to follow the steps in order. Do not skip ahead to source order just because a rule appears later.

---

## Visual Summary

For ordinary author-written CSS, the priority order can be simplified like this:

```text
Highest priority among author CSS
        ↑
Inline !important declarations
        ↑
Stylesheet !important declarations
        ↑
Normal inline styles
        ↑
Normal stylesheet rules with higher specificity
        ↑
Normal stylesheet rules with equal specificity,
but appearing later in source order
        ↑
Lowest priority
```

> **Note:** This diagram is a practical simplification for author CSS. It does not show user-origin rules, user-agent rules, transitions, animations, or cascade layers. **Within the author `!important` tier, inline `!important` still outranks stylesheet `!important`, which is why the diagram splits them into two lines.** Those topics are important for advanced CSS, but they are outside the scope of this guide.

---

## Practice Scenarios

### Scenario 1: Specificity Battle

```html
<h1 class="title" id="main-title">Hello</h1>
```

```css
.title {
  color: blue;
}

#main-title {
  color: green;
}
```

**Answer:** Green.

**Reason:** The ID selector has higher specificity than the class selector.

---

### Scenario 2: Source Order Battle

```html
<h1 class="a-class another-class">Hello</h1>
```

```css
.a-class {
  color: blue;
}

.another-class {
  color: green;
}
```

**Answer:** Green.

**Reason:** Both selectors have the same specificity: one class each. Both are normal author stylesheet rules. The later rule wins.

---

### Scenario 3: Inline Style vs. ID Selector

```html
<h1 id="an-id" style="color: blue;">Hello</h1>
```

```css
#an-id {
  color: green;
}
```

**Answer:** Blue.

**Reason:** The inline style is normal, and a normal inline style beats a normal stylesheet rule regardless of selector specificity.

---

### Scenario 4: External vs. Internal Stylesheet

```html
<link rel="stylesheet" href="style.css">

<style>
  h1 {
    color: blue;
  }
</style>

<h1>Hello</h1>
```

Assume `style.css` contains:

```css
h1 {
  color: green;
}
```

**Answer:** Blue.

**Reason:** Both declarations are normal author stylesheet rules with the same specificity. The internal `<style>` rule appears later in the document, so it wins.

If the `<style>` block appeared before the `<link>`, the external rule would come later and **would win** instead.

---

### Scenario 5: `!important` vs. Inline Style

```css
h1 {
  color: green !important;
}
```

```html
<h1 style="color: red;">Hello</h1>
```

**Answer:** Green.

**Reason:** The `!important` author declaration beats the normal inline style.

---

### Scenario 6: Class vs. Attribute Selector

```html
<p class="note" draggable="true">Text</p>
```

```css
.note {
  color: blue;
}

[draggable] {
  color: purple;
}
```

**Answer:** Purple, because `[draggable]` appears later.

**Reason:** `.note` and `[draggable]` have the same specificity. Source order decides the winner.

---

## Practical Exercise

Consider this HTML:

```html
<div id="outer-box" class="box">
  <div class="box inner-box">
    <p class="white-text">Some text</p>
  </div>
  <div class="box">More content</div>
</div>
```

Suppose the existing CSS is:

```css
.box {
  background-color: blue;
  padding: 10px;
}
```

### Goal 1: Make the Outer Box Purple

Use the ID selector:

```css
#outer-box {
  background-color: purple;
}
```

An ID selector is more specific than a class selector, so it overrides `.box` regardless of source order. This is a reliable solution because specificity, not source order, decides the winner.

### Goal 2: Make the Inner Box Red

Use the unique class `.inner-box`:

```css
.inner-box {
  background-color: red;
}
```

However, order matters here. Both `.box` and `.inner-box` are class selectors, so they have equal specificity. The `.inner-box` rule must appear **after** the `.box` rule:

```css
.box {
  background-color: blue;
}

.inner-box {
  background-color: red;
}
```

If `.inner-box` appeared before `.box`, the later `.box` rule would win.

A more specific selector would avoid the ordering problem:

```css
.box.inner-box {
  background-color: red;
}
```

Now the selector has two classes, so it is more specific than `.box` alone. This solution is more robust because it does not depend on source order.

This exercise reinforces two key lessons:

1. **ID selectors are more specific than class selectors.**
2. **When specificity is equal, source order determines the winner.**

It also shows a practical principle: when you can solve a conflict with specificity, you make your CSS more predictable and less dependent on the order of rules.

---

## Summary

| Cascade Step | What It Means | Key Rule |
|---|---|---|
| **Origin and importance** | Where the CSS comes from and whether it uses `!important` | Author `!important` beats normal author declarations |
| **Inline styles** | Styles written in the `style` attribute | Normal inline styles beat normal stylesheet rules |
| **Specificity** | How precisely a selector targets an element | ID > class/attribute/pseudo-class > element/pseudo-element |
| **Source order** | Where the rule appears in the source | Later rules win when all earlier comparisons are equal |

The CSS cascade is not random. It follows a consistent decision process:

1. Check importance.
2. Check inline styles.
3. Check specificity.
4. Check source order.

When a style is not applying as expected, do not guess. Work through these steps in order. That is how experienced developers debug CSS.

The cascade exists to make CSS predictable. Once you understand it, you can look at any conflict and explain exactly why one declaration won and another lost. That understanding is the foundation for writing maintainable, bug-free stylesheets.
