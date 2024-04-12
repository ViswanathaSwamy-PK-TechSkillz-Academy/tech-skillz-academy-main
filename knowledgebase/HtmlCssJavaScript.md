# HTML, CSS, and JavaScript

This document contains the Knowledge Base from different sources.

## Topics

> 1. [box-sizing: border-box](#box-sizing-border-box)

## Reference(s)

> 1. <https://www.markdownguide.org/basic-syntax/>

## box-sizing: border-box

> 1. box-sizing: border-box; is a CSS property that alters the default CSS box model used to calculate the size of elements.
> 1. In the default CSS box model, the width and height of an element are calculated by adding the content width and height to any padding and borders applied to the element. This means that if you set a width of 100 pixels on an element with padding of 10 pixels and a border of 2 pixels, the total width of the element will be 114 pixels (100 pixels + 10 pixels padding on each side + 2 pixels border on each side).
> 1. However, when you use box-sizing: border-box;, the width and height of an element are calculated differently. With border-box, the width and height you set for an element include the padding and border. This means that if you set a width of 100 pixels on an element with padding of 10 pixels and a border of 2 pixels, the content area of the element will still be 100 pixels wide, and the padding and border will be included within that 100 pixels.

```css
/* Default box model */
.element {
  width: 100px;
  padding: 10px;
  border: 2px solid black;
  /* Total width will be 114px */
}

/* Box model with border-box */
.element {
  width: 100px;
  padding: 10px;
  border: 2px solid black;
  box-sizing: border-box;
  /* Total width will be 100px */
}
```

> In summary, box-sizing: border-box; makes it easier to specify element dimensions, especially when you want to ensure that padding and border do not affect the overall layout. It's commonly used in modern CSS layouts.
