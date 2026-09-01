# Collatz / 10¹⁸

A self-contained demonstration of a variable-height virtual list spanning every integer from `1` through `10¹⁸`.

Open `index.html` directly in a modern browser. There are no dependencies, build steps, or network requests.

## How it works

A normal virtual list relies on a tall spacer element and the browser's native `scrollTop`. That approach breaks down here: the conceptual list is vastly taller than browser element-size limits, and JavaScript `Number` cannot represent every integer up to `10¹⁸` exactly.

Instead, the demo owns its logical scroll position as a pair:

```js
const scroll = {
  index: 1n,       // BigInt: the first visible Collatz entry
  pixelOffset: 0,  // Number: pixels scrolled within that entry
};
```

Only the entries needed to cover the viewport and a small overscan area are rendered. No giant spacer element exists.

### Variable-height entries

Every value in an entry's complete Collatz sequence is displayed on its own line. Entry height is therefore derived from the sequence length:

```text
height = vertical padding + sequence length × line height
```

When scrolling crosses an entry boundary, its height is subtracted from `pixelOffset` and `index` advances. Scrolling backwards performs the inverse operation using the preceding entry's height. Computed Collatz sequences are cached so nearby scrolling does not repeat the work.

### The fake scrollbar

The scrollbar is custom and is not connected to a native scrolling element. Its thumb projects the current `BigInt` index onto a visual range from `0` to `1`. Dragging performs the inverse projection and jumps to the corresponding index.

The global thumb position is intentionally index-based rather than pixel-height-based. Computing the exact total height would require evaluating the Collatz sequence for all `10¹⁸` entries, defeating the point of virtualization. Local scrolling remains pixel-accurate.

### Numeric precision

Entry indices and Collatz values use `BigInt`. This is required because JavaScript integers stored as `Number` stop being exact above `2⁵³ − 1`, well below `10¹⁸`. Ordinary numbers are used only for screen-space pixels and the approximate visual scrollbar ratio.

## Controls

- Scroll with a mouse wheel or trackpad.
- Use arrow keys and Page Up/Page Down after focusing the list.
- Drag or click the custom scrollbar.
- Enter an exact integer in the jump field.
- Use the preset buttons to explore widely separated regions.

## Original prompt

> Create a virtual scrolling demo. This demo should be a self contained HTML page, that has a large, scrollable virtual list. The entry N in this table is the derivation for the number N of the collatz function with entry having the number of successive values from the collatz sequence down to 1. The list should be able to show all entries up to 1e18. The key to making this work is having a virtual scroll div, that draws just the entries around the visible scroll window. The scroll position is stored as a pair: the first is the index of the number shown, the second is pixel offset whithin that entry. A vertical scrollbar can be used to scroll up and down, however this scroll bar is not correctly connected to the div, but rather "fakes" the position by computing its apparent position in the scrolled virtual list.
