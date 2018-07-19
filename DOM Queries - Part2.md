# DOM Queries - Part2

- ~~@ViewChild~~
- ~~@ViewChildren~~
- @ContentChild
- @ContentChildren



## Content Projection (feat. ng-content)

```html
// App Component template
<todo-app>
  <app-footer>
    Yet another todo app!
  </app-footer>
</todo-app>
```

```html
// TodoApp Component template
<ng-content select="app-footer"></ng-content>
```

- With `ng-content`, we can grab the content between the opening and closing tag of the `todo-app` element and project it somewhere inside of the template. 
- `ng-content` supports a `select` attribute which enables you to select the content and project it in specific places. 
- `select` attribute takes a CSS selector(element, .class, [attribute]) to match the content you want.
- You can skip the `select` attribute of the `ng-content` element and then it will serve as a catch-all and will receive all children that did not match any of the other `ng-content` elements.
- Targeted `ng-content` (with the `select` attribute) takes precedence over the non-select attribute one, even if it's specified after in the template.



### ngProjectAs

For whatever reason, it often happens that your inner component is not a direct child of the wrapper:

```html
<wrapper>
	<ng-container>
    	<your-inner-component></your-inner-component>
    </ng-container>
</wrapper>
```

- With the above template, `ng-content` in `WrapperComponent`'s template cannot catch it via:

  ```html
  <ng-content select="your-inner-component"></ng-content>
  ```

   because the `ng-container` around it doesn't match the `select` attribute.

- To make it work as you intend, use `ngProjectAs` attribute, which can be put on any element and lets it *disguise* any element for content projection purposes.

- It takes the exact same kind of selectors as the `select` attribute on `ng-content`.

Fixed template:

```html
<wrapper>
  <ng-container ngProjectAs="your-inner-component">
      <your-inner-component></your-inner-component>
  </ng-container>
</wrapper>
```



### The Behaviour/Feature of ng-content

- `ng-content` doesn't *produce* content, it simply projects existing content.
- The lifecycle of the projected content is bound to where it is **declared**, not where it is displayed.



## @ContentChild/@ContentChildren

```html
// App Component template
<todo-app>
  <app-footer>
    Yet another todo app!
  </app-footer>
</todo-app>
```

```javascript
@Component({
    selector: 'todo-app',
    template: '<ng-content select="app-footer"></ng-content>'
})
class TodoAppComponent implements AfterContentInit {
  	@ContentChild(FooterComponent) footer: FooterComponent;
  
	ngAfterContentInit() {
    	// this.footer now points to the instance of `FooterComponent`
  	}
}

@Component({
	selector: 'app-footer',
	template: '<ng-content></ng-content>'
})
class FooterComponent {}
```

- Any element or component  which is projected inside `<ng-content>` is accessed as  `@ContentChild`.
- As you might notice, the difference between accessing `@ViewChild` and `@ContentChild` is the lifecycle hooks in which they are initialized: `ngAfterViewInit` vs `ngAfterContentInit`
- `@ContentChild` returns one child and `@ContentChildren` returns a `QueryList` (Same as `@ViewChild`/`@ViewChildren`).



## References

- [ViewChildren & ContentChildren](https://codecraft.tv/courses/angular/components/viewchildren-and-contentchildren/)
- [Simplifying ViewChild and ContentChild in Angular](https://www.infragistics.com/community/blogs/b/infragistics/posts/simplifying-viewchild-nad-contentchild-in-angular)
- [Understanding @ViewChildren, @ViewChild, @ContentChildren and @ContentChild](https://medium.com/@tkssharma/understanding-viewchildren-viewchild-contentchildren-and-contentchild-b16c9e0358e)
- [ng-content: The hidden docs](https://medium.com/claritydesignsystem/ng-content-the-hidden-docs-96a29d70d11b)