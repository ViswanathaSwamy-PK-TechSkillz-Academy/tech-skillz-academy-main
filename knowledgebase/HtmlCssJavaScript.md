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

## 06-Jun

Your code is functional, but there are a few improvements that can make it more efficient, maintainable, and readable.

### JavaScript Improvements

1. **Avoid `innerHTML` for updating count**: Using `innerHTML` to update the count is not recommended for simple text updates. Instead, use `textContent`.
2. **Use `parseInt` to ensure the count is treated as a number**: `count.textContent` returns a string, so parsing it to an integer is necessary.
3. **Delegate event listener more efficiently**: Adding event listeners to the parent container and using event delegation is efficient, but ensure the event target is correctly identified.
4. **Optimize `setColor` function**: Directly compare numeric values instead of relying on type coercion.

### Updated JavaScript Code

```javascript
document.addEventListener("DOMContentLoaded", () => {
  const count = document.querySelector(".count");
  const buttons = document.querySelector(".buttons");

  buttons.addEventListener("click", (e) => {
    if (e.target.closest("button")) {
      const button = e.target.closest("button");

      if (button.classList.contains("add")) {
        count.textContent = parseInt(count.textContent) + 1;
      } else if (button.classList.contains("subtract")) {
        count.textContent = parseInt(count.textContent) - 1;
      } else if (button.classList.contains("reset")) {
        count.textContent = 0;
      }

      setColor();
    }
  });

  function setColor() {
    const currentCount = parseInt(count.textContent);
    if (currentCount > 0) {
      count.style.color = "yellow";
    } else if (currentCount < 0) {
      count.style.color = "orangered";
    } else {
      count.style.color = "#fff";
    }
  }
});
```

### HTML Improvements

The HTML is mostly fine, but ensure you have the `DOMContentLoaded` event listener in JavaScript to ensure the DOM is fully loaded before your script runs. You can also ensure semantic HTML by using appropriate elements.

### CSS Improvements

The CSS is well-organized, but here are a few suggestions:

1. **Define button colors in variables**: This improves maintainability.
2. **Consistent styling**: Ensure consistent styling and spacing for better readability.

### Updated CSS Code

```css
@import url("https://fonts.googleapis.com/css2?family=Open+Sans:wght@300;400;600;700&display=swap");

:root {
  --white: #fff;
  --black: #1c2b2d;
  --blue: #31326f;
  --light-blue: #005490;
  --color-primary: #9d0191;
  --color-sec: #db6400;
  --grey: #eee;
  --dark-grey: #222831;
  --yellow: yellow;
  --orangered: orangered;
  --green: green;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html {
  font-size: 10px;
}

body {
  font-family: "Open Sans", sans-serif;
}

p {
  font-size: 1.6rem;
  line-height: 1.5;
}

img {
  width: 100%;
}

.container {
  max-width: 900px;
  margin: 0 auto;
  padding: 0 20px;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.counter-div {
  width: 42rem;
  border: 1px solid var(--grey);
  padding: 1rem;
  background-color: var(--light-blue);
  color: var(--white);
  text-align: center;
  border-radius: 5px;
}

.counter-div > * {
  margin-bottom: 2rem;
}

.count {
  font-size: 4rem;
}

.buttons button {
  padding: 1rem;
  margin: 0 5px;
  font-size: 1.5rem;
  color: var(--white);
  font-weight: 700;
  border: none;
  outline: none;
  border-radius: 3px;
  cursor: pointer;
}

button.subtract {
  background-color: var(--orangered);
}

button.reset {
  background-color: var(--white);
  color: var(--black);
}

button.add {
  background-color: var(--green);
}
```

### Final Notes

- Using `textContent` instead of `innerHTML` prevents potential security issues and is more performant for text updates.
- Parsing the count as an integer ensures accurate arithmetic operations.
- Using CSS variables for colors helps maintain consistency and makes future updates easier.
-
