# CSS Theming

Our theme will be made of the following layers:

1. **variables** the unique values to use in the next layers;
2. **base theme** this layer we declare global variables and style to be used
   by all the application;
3. **component theme** the structure of this layer will be made in every component.

## Variables

We declare variable for the following reasons:

- To store the variables and remember them values;
- To compare values and avoid duplications or similarities;
- To be the source of truth to change a value, for example to adjust some contrast
  or change the main font family.

The definition of this rule is made by those responsible for the brand, that is,
who will decided the brand logo colors and message of the company. The developer
in this step should only put this variables in the code, directly or by tokens.

Tokens is a way to define useful cross-platform variables, this way use the same
value in every technology. [See more about Design Tokens.](https://spectrum.adobe.com/page/design-tokens/)

Below and example of definition of this variables in pure CSS:

```css
/* variables.css */
:root {
  --color-primary: #22bb44;
  --color-dark: #222;
  --color-dark-secondary: #222;
  --color-light: #f1f1f1;
  --color-white: #fff;

  --font-family: 'Times New Roman';
  --font-size-regular: 14px;
  --font-size-large: 22px;
}
```

## Base theme

Now with the variables in our hands we can create a Base Theme to drive the application.
This layer is important to be the base of all reusable and common in the application.

The componentes will inherit this atributes, so beware to made them generic enough.

In the example below the styles are in the body tag where we define basic styles
of the page. First we start declaring the theme and after the base appearance.

```css
/* theme.css */
body {
  --theme-color: var(--color-dark);
  --theme-font: var(--font-size-regular) var(--font-family);
  --theme-bg: var(--color-white);

  --theme-title-color: var(--color-dark-secondary);
  --theme-title-font: var(--font-size-large) var(--font-family);

  --theme-action-color: var(--color-primary);
  --theme-action-color-contrast: var(--color-white);

  --theme-border-color: var(--color-light);
}

/* reset.css */
body {
  background-color: var(--theme-bg);
  color: var(--theme-color);
  font: var(--theme-font);
}
```

We define in this layer some patterns of the application, the componentes of the
application can use this values.

### Component theme

If we made all right the layer of the component will inherit properties to use.
In the componente we don't need to necessarily declare itself variable, just use.

```css
button {
  background: var(--button-bg, var(--theme-action-color));
  color: var(--button-color, var(---theme-action-color-contrast));
}
```

If you see, the default button background is `--theme-action-color`, but if we
declare `--button-bg` the button will change its color.

```css
.paper {
  --button-bg: var(--paper-button-bg, var(--color-dark));

  background: var(--paper-bg, var(--color-light));
}
```

To declare the `--button-bg` will override the button color, but still you don't
define the final value to the button, because the paper also can receive an
overriden value in `--paper-button-bg`. See the example with three levels:

```css
button {
  background: var(--button-bg, var(--theme-action-color));
}

.paper {
  --button-bg: var(--paper-button-bg, var(--color-dark));
}

.notebook {
  /* change background of button in the notebok */
  --button-bg: var(--notebook-button-bg, var(--color-dark-variant));
  /* change background of button in the paper in the notebook */
  --paper-button-bg: var(--notebook-paper-button-bg, var(--color-light));
}
```

Or else, we might change the "action color" in this context then all components
that inherit the "action color" will change too.

```css
.paper {
  --theme-action-color: var(--color-dark);

  background: var(--paper-bg, var(--color-light));
}
```

If you notice well, we've overriden the background color without increase the
strenght of the selector, diferent to do `.paper button`.

**Attention:** do not declare component variables in root elements because you
can't override if you wish.

```css
/* theme.css */
body {
  --theme-action-color: var(--color-primary);

  /* this is a mistake */
  --button-bg: var(--theme-action-color);
}

button {
  background: var(--button-bg);
  color: var(--color-white);
}

.container {
  /* because if you set another action color the --button-bg will not change
  since it was defined with other value  */
  --theme-action-color: var(--color-dark);
}
```

Wrong

| theme                | parent component | component     |
| -------------------- | ---------------- | ------------- |
| X = old value, Y = X |                  | X = old value |
| X = old value, Y = X | X = Z            | X = old value |

Right

| theme          | parent component | component     |
| -------------- | ---------------- | ------------- |
| X = old value, |                  | Y = old value |
| X = old value  | X = new value    | Y = new value |

Did you got it? If you want to define a component theme that inherit properties
you need keep this cascade of information.

## Component Layers

Now with our structure of variables we can think in the definition of our project
and components structure.

First of all we need to understand the concept of [CSS Layers](https://developer.mozilla.org/en-US/docs/Web/CSS/@layer). The Rule `@layer` create a level of CSS lower than
regular CSS out of any `@layer`.

We can create many layers and define the priority of one over other. Starting with
these initial layers:

```css
@layer base, component;
```

We can say that everything declared like a component is more important than a base
definion, and either every style out of any layer will be higher priority than
any layer style. With this concept we can make our layers of base definition and
components with lower priority than the aplication itself, how we can see in the
following example:

```css
@layer base, component;

@layer base {
  body {
    --theme-color: var(--color-dark);

    color: var(--theme-color);
  }
}
```
