# Directive in Angular

- The Angular team recommends using directives as *attributes*, prefixed with a namespace, e.g. qq(namespace)DirectiveName.

- The selector attribute uses *CSS matching rules* to match a component/directive to a HTML element.

-  If I write the selector as `.ccCardHover`:

  ```javascript
  import { Directive } from '@angular/core';
  .
  .
  .
  @Directive({
    selector:".ccCardHover"
  })
  class CardHoverDirective { }
  ```

  Then this would associate the directive with any element that has a *class* of ccCardHover:

  ```javascript
  <div class="card card-block ccCardHover">...</div>
  ```

- We mostly want to associate the directive to an element which has a certain *attribute*. To do that in CSS we wrap the name of the attribute with `[]`, and this is why the selector is called `[ccCardHover]`.

## Directive Constructor

```javascript 
import { ElementRef, Renderer2 } from '@angular/core';
.
.
.
class CardHoverDirective {
  constructor(private el: ElementRef.
              private renderer: Renderer2) {
      renderer.setElementStyle(el.nativeElement, 'backgroundColor', 'gray');
  }
}
```

- The `ElementRef` gives the directive *direct access* to the DOM element upon which it’s attached.
- `ElementRef` itself is a wrapper for the actual DOM element which we can access via the property `nativeElement`.
- `Renderer2` is what Angular provides as a platform independent way of manipulating DOM.
- For exmaple, `Renderer2` calls the appropriate functions to change the background color on the platform which it runs upon.

## @HostListener

- This is a *function* decorator that accepts an *event* name as an argument. When that event gets fired on the *host* element, it calls the associated function:

  ```javascript
  @HostListener('mouseover') onMouseOver() { 
      let part = this.el.nativeElement.querySelector('.card-text') 
      this.renderer.setElementStyle(part, 'display', 'block'); 
  }
  ```

## @HostBinding



## References

- [Custom Directives](https://codecraft.tv/courses/angular/custom-directives/hostlistener-and-hostbinding/)