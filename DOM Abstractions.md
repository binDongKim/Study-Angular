# DOM Abstractions in Angular

DOM Abstractions come in a form of the following reference types:

- ElementRef
- TemplateRef
- ViewRef
- ComponentRef
- ViewContainerRef



## @ViewChild / @ViewChildren

`@ViewChild/@ViewChildren` is the way Angular provides for us to access those abstractions inside a component/directive class.

The basic syntax to `ViewChild` decorator is the following:

```javascript
@ViewChild([reference from template], {read: [reference type]});
```

The second parameter `read` is not always required because Angular can infer it by the type of the DOM element. For example, if it's a simple html element like `span`, Angular returns `ElementRef`. Some references like `ViewContainerRef` cannot be inferred so you should ask it specifically by declaring the `read` parameter with the type.



## ElementRef

This is the most basic abstraction. It only holds the native element that it's associated with.

- It's useful for accessing native DOM element like below:

```javascript
console.log(this.referenceFromTemplate.nativeElement);
```

- It can be also used to get the host element for a component:

```javascript
@Component({
    selector: 'sample',
    ...
export class SampleComponent{
    constructor(private hostElement: ElementRef) {
        //outputs <sample>...</sample>
        console.log(this.hostElement.nativeElement.outerHTML);
    }
```

- Or also in the directive to access the element that the directive is attached to.

- Try to avoid to manipulate DOM via `nativeElement` property of `ElementRef` directly, like:

  ```javascript
  el.nativeElement.style.backgroundColor = "gray";
  ```

  The above code assumes that our application will always be running in the environment of a browser.

  Angular has been built from the ground up to work in a number of different environments, including server side via node and on a native mobile device. So the Angular team has provided a *platform independent* way of setting properties on our elements via something called a `Renderer2`. I will cover `Renderer2` in another section.



## TemplateRef

*I will cover this later.*



## ViewRef

*I will cover this later.*



## ViewContainerRef

It represents a container where one or more views can be attached.

**A few things to note**:

- Any DOM element can be used as a view container.
- Angular doesn't insert views inside the element, but appends them after the element bound to `ViewContainer`.
- `ng-container` could be considered as a good option to mark a place for a view container. It's rendered as a comment so it doesn't leave any unnecessary html elements in DOM.



## References

- [Exploring DOM Abstractions in Angular](https://blog.angularindepth.com/exploring-angular-dom-abstractions-80b3ebcfc02)

