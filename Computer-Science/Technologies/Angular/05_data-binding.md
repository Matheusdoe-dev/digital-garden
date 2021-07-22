# Data binding

- `Data binding` is the process of pass data between the component class and the template.

## Property binding

- Property binding in Angular helps you set values for properties of HTML elements or directives. With property binding, you can do things such as toggle button functionality, set paths programmatically, and share values between components.

- Property binding is a one-way binding, that is, moves a value in one direction, from a component's property into a target element property.

```html
<img [src]="itemImageUrl" />
```

## Attribute, class, and style bindings

- Attribute binding in Angular helps you set values for attributes directly. With attribute binding, you can improve accessibility, style your application dynamically, and manage multiple CSS classes or styles simultaneously.

```html
<p [attr.attribute-you-are-targeting]="expression"></p>
```

### Binding ARIA attributes

- One of the primary use cases for attribute binding is to set ARIA attributes

```html
<!-- create and set an aria attribute for assistive technology -->
<button [attr.aria-label]="actionName">{{ actionName }}</button>
```

### Binding to the class attribute

- You can use class binding to add and remove CSS class names from an element's `class` attribute.

#### Binding to a single CSS class

- `[class.sale]="onSale"`

#### Binding to multiple CSS classes

- `[class]="classExpression"`

### Binding to the style attribute

- You can use style binding to set styles dynamically

- You can write a style property name in either `dash-case`, or `camelCase`

```html
<nav [style.background-color]="expression"></nav>

<nav [style.backgroundColor]="expression"></nav>
```

## References

- [Angular IO](https://angular.io)
- [Loiane Training](https://loiane.training)
