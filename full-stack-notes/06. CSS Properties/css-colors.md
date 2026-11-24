# CSS Colors

## Contents

1. [Why Colors Matter](#1-why-colors-matter)
2. [The Two Properties You Will Use Most](#2-the-two-properties-you-will-use-most)
3. [How Screens Display Color: The RGB Model](#3-how-screens-display-color-the-rgb-model)
4. [Named Colors](#4-named-colors)
5. [Hex Codes](#5-hex-codes)
6. [RGB Functional Notation](#6-rgb-functional-notation)
7. [Finding Color Palettes: Color Hunt](#7-finding-color-palettes-color-hunt)
8. [Quick Reference: Color Formats](#8-quick-reference-color-formats)

---

## 1. Why Colors Matter

Color is one of the first things a visitor notices. It sets the tone of a page, establishes visual hierarchy, and strongly affects readability. A well-chosen palette can make a page feel professional and cohesive; a poor one can make it feel chaotic or hard to read.

CSS offers several ways to specify a color, and each format has its strengths. Knowing them helps you build a consistent palette and communicate precisely with designers and other developers. More importantly, understanding *why* these formats exist — and how they relate to one another — makes it much easier to choose the right one for any given situation.

## 2. The Two Properties You Will Use Most

Most color work in CSS is done with just two properties:

- **`color`** — sets the **text color** of an element.
- **`background-color`** — sets the **background color** painted in the element's background painting area.

By default, the background extends to the border box (`background-clip: border-box`), so it also extends under the border area; an opaque border will cover it. This is why a background color can appear to "fill" the entire element, even the part behind its border.

```css
body {
  background-color: red;
}

h1 {
  color: blue;
}
```

The `body` element contains the page's content, so setting its `background-color` normally colors the entire page when the root `html` element has no background of its own. (Strictly speaking, the `body` background can propagate to the page canvas; see MDN for the exact rules.) The `h1` rule changes only the heading's text color.

> **Tip:** When you are unsure what a property does, check **MDN Web Docs** (developer.mozilla.org). It is a highly reliable, widely used reference for CSS; for normative details, consult the W3C CSS specifications.

## 3. How Screens Display Color: The RGB Model

Before we look at the different ways to *write* a color in CSS, it helps to understand how screens actually *produce* color. This is the foundation for everything that follows.

Screens display color by mixing three primary colors: **red**, **green**, and **blue** — often abbreviated as **RGB**. Each channel has a value from `0` to `255`:

- `0` means none of that color.
- `255` means the maximum amount of that color.

By combining different amounts of red, green, and blue light, a screen can produce millions of distinct colors. For example, the purple `#5D3891` corresponds to:

- Red: 93 out of 255
- Green: 56 out of 255
- Blue: 145 out of 255

The formats covered in this guide — named colors, hex codes, and `rgb()` — are ultimately different notations for the same three sRGB color channels. Hex codes and `rgb()` can also carry an optional alpha channel. Once you understand that, the different formats stop feeling arbitrary — they are simply different ways of writing the same underlying color information.

## 4. Named Colors

CSS Color 4 defines 148 **named colors** (including aliases such as `gray`/`grey`) — human-readable keywords such as `red`, `blue`, `cornflowerblue`, `cadetblue`, `dimgrey`, and `olivedrab`.

- **Advantage:** readability. `background-color: papayawhip;` is immediately understandable.
- **Disadvantage:** you are limited to a fixed set of names.

Named colors are ideal for quick prototypes and for code that needs to be easily readable. But when you need a specific shade that has no name — for example, a particular color from a designer's palette — you will need one of the numeric formats described below.

To see the full list, search for "MDN named colors". The MDN table also shows the equivalent hex code for each name, which is useful when converting between formats.

> **Note on CSS Levels:** You may see colors labeled "CSS Level 1", "Level 2", and so on. These labels indicate the CSS specification level in which a color is defined; they are not a precise history of when each color became usable in browsers. You do not need to worry about levels — just use what works in the browsers you are targeting.

## 5. Hex Codes

When you need a color that has no name — for example, a specific shade from a designer's palette — use a **hex code**:

```css
background-color: #5d3891;
```

A hex code begins with `#` (required) followed by three, four, six, or eight hexadecimal digits. The most common form is six digits; the other forms are shorthands or include transparency, as described below.

### How Hex Codes Relate to RGB

In its six-digit form, a hex code is simply a different way of writing the same three RGB channel values, using base-16 (hexadecimal) instead of base-10. Each pair of characters represents one channel:

| Channel | Decimal | Hex |
|---------|---------|-----|
| Red     | 93      | 5D  |
| Green   | 56      | 38  |
| Blue    | 145     | 91  |

Put them together: `#5D3891`. Case does not matter — `#5d3891` and `#5D3891` are the same color. The `#` prefix tells the browser that the following characters are a hex color.

You do not need to convert between RGB and hex by hand — design tools and color pickers do it for you. What matters is that you **recognize** a hex code and know that, in its six-digit form, it represents a precise color defined by three channel values. The four-digit form adds an alpha channel to the three-digit shorthand, and the eight-digit form adds an alpha channel to the six-digit form.

### Other Hex Forms

The six-digit form (`#RRGGBB`) is the one you will use most. CSS also supports three additional hex forms:

| Form | Example | Meaning |
|------|---------|---------|
| `#RGB` | `#F00` | Shorthand for `#FF0000` (each digit is doubled) |
| `#RGBA` | `#F008` | Like `#RGB`, plus an alpha (transparency) channel |
| `#RRGGBBAA` | `#FF000080` | Six-digit color plus an alpha channel |

The alpha channel controls transparency: in `#RRGGBBAA`, `00` is fully transparent and `FF` is fully opaque. In `#RGBA`, the single alpha digit is expanded in the same way (`0` → `00`, `F` → `FF`). This guide focuses on the six-digit form because it is the most common; the other forms are worth recognizing when you encounter them.

## 6. RGB Functional Notation

CSS also lets you write RGB values directly, using the `rgb()` function:

```css
color: rgb(93, 56, 145);
```

This is the functional equivalent of `#5D3891`. Both describe the same color. The hex form is more compact; the `rgb()` form makes the three channels explicit, which can be easier to read when you are computing values dynamically.

CSS supports alpha values through the widely supported `rgba()` function and the newer `rgb(... / ...)` syntax:

```css
color: rgba(93, 56, 145, 0.5);
/* or the newer syntax: */
color: rgb(93 56 145 / 0.5);
```

An alpha value of `0` is fully transparent; `1` is fully opaque. This is the same alpha channel that the four- and eight-digit hex forms provide, just written differently.

> **Tip:** `rgb(93, 56, 145)` and `#5D3891` are interchangeable. Use whichever makes your code clearer in context.

## 7. Finding Color Palettes: Color Hunt

The built-in named colors are limited. For a more distinctive, harmonious palette, a free tool such as **colorhunt.co** is excellent. It offers professionally designed palettes — groups of four colors that look good together.

In a typical palette, you might assign:

- one color as the page `background-color`
- one as the `h1` text color
- one as the `h2` or subtitle color
- one as an accent color for buttons or links

Each color in a Color Hunt palette is shown with its hex code. Copy the hex code and paste it into your CSS:

```css
h1 {
  color: #f9f9f9;
}
```

You can also find hex codes next to each named color in the MDN documentation, so you can switch between formats freely.

## 8. Quick Reference: Color Formats

| Format | Example | When to Use |
|--------|---------|-------------|
| Named color | `red`, `cornflowerblue` | Quick prototypes, readable code |
| Hex code | `#5D3891` | Custom palettes, design tools |
| RGB (functional) | `rgb(93, 56, 145)` | When you need to write channel values explicitly, compute them dynamically, or use the functional alpha syntax |

> **Key takeaway:** Named colors are convenient and readable. Hex codes give you access to any 8-bit sRGB color. RGB functional notation makes the channel values explicit and pairs naturally with transparency. All three formats are valid `<color>` values in modern browsers. The named colors describe opaque sRGB colors; `transparent` is a separate special keyword. Hex codes and `rgb()` describe the same sRGB channels and can optionally include an alpha channel.
