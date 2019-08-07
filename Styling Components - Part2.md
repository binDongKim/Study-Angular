# Styling Components - Part2

## ViewEncapsulation

- Emulated(default): No Shadow DOM but style encapsulation emulation.
- Native: Native Shadow DOM
- None: No Shadow DOM at all. Therefore, no style encapsulation.



### Example

```javascript
// component
import {ViewEncapsulation} from '@angular/core';

@Component({
  moduleId: module.id,
  selector: 'my-zippy',
  templateUrl: 'my-zippy.component.html',
  styles: [`
    .zippy {
      background: green;
    }
  `],
  // encapsulation: ViewEncapsulation.None || ViewEncapsulation.Emulated || ViewEncapsulation.Native
})
class ZippyComponent {
  @Input() title: string;
}
```

```html
<!-- template -->
<div class="zippy">
  <div (click)="toggle()" class="zippy__title">
     
  </div>
  <div [hidden]="!visible" class="zippy__content">
    <ng-content></ng-content>
  </div>
</div>
```



### ViewEncapsulation.Emulated

The styles will be written to the document head but with specific selector:

```html
<head>
  <style>.zippy[_ngcontent-1] {
  	background: green;
  }</style>
</head>
```

Also, the template will look different:

```html
<my-zippy title="Details" _ngcontent-0 _nghost-1>
  <div class="zippy" _ngcontent-1>
    <div (click)="toggle()" class="zippy__title" _ngcontent-1>
      ▾ Details
    </div>
    <div [hidden]="!visible" class="zippy__content" _ngcontent-1>
      <script type="ng/contentStart" class="ng-binding"></script>
        ...
      <script type="ng/contentEnd"></script>
    </div>
  </div>
</my-zippy>
```

Angular adds some attributes to our component's template as well in order to implement some sort of *scoped style*. Therefore, it won't collide with other selectors existing in different places.



### ViewEncapsulation.Native

The styles will be in the component's template inside the shadow root:

```html
<my-zippy title="Details">
  #shadow-root
  | <style>
  |   .zippy {
  |     background: green;
  |   }
  | </style>
  | <div class="zippy">
  |   <div (click)="toggle()" class="zippy__title">
  |     ▾ Details
  |   </div>
  |   <div [hidden]="!visible" class="zippy__content">
  |     <content></content>
  |   </div>
  | </div>
  "This is some content"
</my-zippy>
```



### ViewEncapsulation.None

The styles will be written to the document head without any specific additional selector:

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      .zippy { 
        background: green;
      }
    </style>
  </head>
  <body>
    <my-zippy title="Details">
      <div class="zippy">
        <div (click)="toggle()" class="zippy__title">
          ▾ Details
        </div>
        <div [hidden]="!visible" class="zippy__content">
          <script type="ng/contentStart"></script>
            ...
          <script type="ng/contentEnd"></script>
        </div>
      </div>
    </my-zippy>
  </body>
</html>
```

This means the styles at this time affect the entire document. 

**Note**

When you use third-party libraries and you want to change some styles of it, you can't achieve it with `ViewEncapsulation.Emulated(default)` setting because the elements under the library literally locate under the library so the default setting won't be matched. Therefore, you may want to go for `::ng-deep` pseudo-class selector or set ViewEncapsulation option to `None`. 



## References

- https://blog.thoughtram.io/angular/2015/06/29/shadow-dom-strategies-in-angular2.html

