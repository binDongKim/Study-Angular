# Styling Components

Example:

```javascript
@Component({
    selector: 'app-root',
    styleUrls:['./app.component.css'],
    template: `
        <button class="red-button">Button</button>
		<blue-button></blue-button>
    `})
export class AppComponent {

   ...
}

@Component({
  selector: 'blue-button',
  template: `
      <h2>Blue button component</h2>
      <button class="blue-button">Button</button>
  `,
  styles: [`
      .blue-button {
          background:blue;
      }
  `]
})
export class BlueButtonComponent  {
}
```



Interpreted HTML:

```html
<app-root _nghost-c0="">

    <button _ngcontent-c0="" class="red-button">Button</button>

    <blue-button _nghost-c1="" _ngcontent-c0="">
      
        <h2 _ngcontent-c1="">Blue button component</h2>

        <button _ngcontent-c1="" class="blue-button">Button</button>
      
  </blue-button>

</app-root>
```

**How the host and template element properties work**

- Upon application startup, each component will have a unique property attached to the host element, depending on the order in which the components are processed: `_nghost-c0`, `_nghost-c1`, etc.
- Each element inside of each component template will also get a property applied that is unique to that particular component: `_ngcontent-c0`, `_ngcontent-c1`, etc



## How do Angular encapsulate styles?

At startup time(or at build time if using AOT), Angular will see what styles are associated with which components via the `styles` or `styleUrls` component properties. Then it will take those styles and apply them transparently to the corresponding isolating properties, and then it will inject the styles directly into the page header as a `style` tag:

```css
<style>
  .blue-button[_ngcontent-c1] {
      background:blue;
   }
</style>
```

The `_ngcontent-c1` property is unique to elements of the `blue-button` template, so the style will be scoped to those elements only.



## The `:host` pseudo-class selector

- Use it when you want to style the component custom HTML element itself.

- ```css
  :host {
      border: 2px solid dimgray;
      display: block;
      padding: 20px;
  }
  ```

  will be interpreted at runtime as:

  ```css
  <style>
    [_nghost-c0] {
      border: 2px solid dimgray;
      display: block;
      padding: 20px;
    }
  </style>
  ```

- The use of the special `_nghost-c0` will ensure that those styles are only applied to the `app-root` element because `app-root` gets that property applied at runtime:

  ```html
  <app-root _nghost-c0="">
    ...
  </app-root>
  ```

- This following style would be applied only when the host has the specified CSS class:

  ```css
  :host(.className) {
      width: 500px;
  }
  ```


## Combining the `:host` selector with other selectors

```css
:host h2 {
    color: red;
}
```

The above style will be only applied to the `h2` elements inside of the `app-root` template, but **not** to the `h2` inside of the `blue-button` component.

It's because the above style will be interpreted as:

```css
<style>
  [_nghost-c0]   h2[_ngcontent-c0] {
    color: red;
  }
</style>
```

You can see that the special scoping property gets applied to the nested selectors as well to ensure the style is always scoped to that particular template.



## The `::ng-deep` pseudo-class selector

If we want our component styles to cascade to all child elements of a component, but not to any other element on the page, `::ng-deep` selector is what you are looking for:

```css
:host ::ng-deep h2 {
    color: red;
}
```

This will generate a style at runtime like this:

```css
<style>  
    [_nghost-c0]  h2 {
        color: red;
    }
</style>
```

> marked as **deprecated** soon on the official document but you can still use it.



## The `:host-context` pseudo-class selector

Sometimes, it's useful to apply styles based on some condition *outside* of a component's view. For instance, a CSS theme class could be applied to the document `<body>` element, and you may want to change how your component looks based on that. The `:host-context` selector looks for a CSS class in any ancestor of the component host element, up to the document root.

The following example applies a `background-color` style to all `<h2>` elements *inside* the component only if some ancestor element has the CSS class name ~~theme-light~~:

```css
:host-context(.theme-light) h2 {
    background-color: #eef;
}
```

Common use case: **Theme enabling classes**

```javascript
@Component({
  selector: 'themeable-button',
  template: `
        <button class="btn btn-theme">Themeable Button</button>
  `,
  styles: [`
      :host-context(.red-theme) .btn-theme {
        background: red;
      }
      :host-context(.blue-theme) .btn-theme {
          background: blue;
      }
  `]
})
export class ThemeableButtonComponent {
}
```

The above styles won't work till we add any *theme activating classes* to its parents component element:

```html
<div class="blue-theme">
    <themeable-button></themeable-button>
</div>
```



## References

- [Angular :host, :host-context, ::ng-deep - Angular View Encapsulation](https://blog.angular-university.io/angular-host-context/)