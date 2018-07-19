# DOM Queries - Part1

- @ViewChild
- @ViewChildren
- ~~@ContentChild~~
- ~~@ContentChildren~~



## @ViewChild

### Query a plain DOM element using @ViewChild

```html
<h1 #title>ViewChild Test</h1>
```

```javascript
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html'
})
export class AppComponent implements AfterViewInit {
  @ViewChild('title') title: ElementRef;

  ngAfterViewInit() {
     console.log('Values on ngAfterViewInit():');
     console.log("title:", this.title.nativeElement);
  }
}
```

- `@ViewChild` returns the first element that matches the selector.
- When we query a plain DOM element using `@ViewChild`, Angular returns the `ElementRef` DOM abstraction which wraps the native DOM element.



### Query a component element using @ViewChild

#### 1. Default Behaviour

```html
<some-component #someComponent></some-component>
```

```javascript
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html'
})
export class AppComponent implements AfterViewInit {
  @ViewChild('someComponent') sample: SomeComponent;

  ngAfterViewInit() {
    console.log('Values on ngAfterViewInit():');
    console.log("sample:", this.sample);
  }
}
```

- When we query a component element using `@ViewChild`, Angular returns the component's instance.

- We can directly access to the injected component's properties and methods.

- It works the same as:

  ```html
  <some-component></some-component>
  ```

  ```javascript
  @Component({
    selector: 'app-root',
    templateUrl: './app.component.html'
  })
  export class AppComponent implements AfterViewInit {
    @ViewChild(SomeComponent) sample: SomeComponent;
  
    ngAfterViewInit() {
      console.log('Values on ngAfterViewInit():');
      console.log("sample:", this.sample);
    }
  }
  ```

  The `sample` variable is the same one linked to the `<some-component></some-component>` present in the template.



#### 2. With @ViewChild options argument

```html
<some-component #someComponent></some-component>
```

```javascript
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html'
})
export class AppComponent implements AfterViewInit {
  @ViewChild('someComponent', {read: ElementRef}) sample: ElementRef;

  ngAfterViewInit() {
    console.log('Values on ngAfterViewInit():');
    console.log("sample:", this.sample.nativeElement);
  }
}
```

- You pass the second argument containing a configuration object with the `read` property set to `ElementRef`
- The `read` property will specify what you are trying to query, in case that there are multiple possible options.
- In this case, we are using the `read` property to specify that we want to get the DOM element(wrapped by `ElementRef`) of the matched template reference, not the component.



*Side note: If you want to make sure that the references queried by `@ViewChild` are available, you should always write your initialization code using `ngAfterViewInit()`. The references might already be present on  `ngOnInit()`, but it doesn't always work like that.*



### Visibility scope of @ViewChild template queries

`@ViewChild` can **ONLY** query the elements inside of the template of the component itself. In other words, it cannot query either:

- Elements inside of the templates of its child components

or

- Elements inside of the templates of its parent components



**To summarize:**

> `@ViewChild` decorator is a template querying mechanism that is local to the component.



### @ViewChild with Directive

`@ViewChild` can instantiate a directive within a component and then the component will be able to access the directive's methods.

```javascript
import { Directive, ElementRef } from '@angular/core';

@Directive({ 
     selector: '[cpColor]' 
})
export class CpColorDirective {
    constructor(
    	private elRef: ElementRef
    ) {}
    
    change(changedColor: String) {
	   this.elRef.nativeElement.style.color = changedColor;
    }
} 
```

```html
<p cpColor>Change my Color </p>
<div>
  Change Color:
  <input type="radio" name="rad" (click)= "changeColor('green')"> Green
  <input type="radio" name="rad" (click)= "changeColor('cyan')"> Cyan
  <input type="radio" name="rad" (click)= "changeColor('blue')"> Blue
</div> 
```

```javascript
import { Component, ViewChild } from '@angular/core';
import { CpColorDirective } from './cpcolor.directive';

@Component({
    selector: 'app-cpcolor-parent',
    templateUrl: './cpcolor-parent.component.html'
})
export class CpColorParentComponent {
    @ViewChild(CpColorDirective)
    private cpColorDirective: CpColorDirective;
    
	changeColor(color: string) {
        this.cpColorDirective.change(color);
    }
}
```



### Why you don't always need @ViewChild

Many simple interactions can be coded directly in the template using template references without the need to use the component class.



## @ViewChildren

```javascript
@Component({
  selector: 'todo-app',
  template: `...`
})
class TodoAppComponent implements AfterViewInit {
  @ViewChildren(TodoComponent) todoComponents: QueryList<TodoComponent>;

  constructor(
      private todos: TodoList
  ) {}
  ngAfterViewInit() {
    // available here
  }
}
```

- When you want multiple elements to query, in this case, `TodoComponent`, use `@ViewChildren`.
- The `@ViewChildren` decorator supports directive or component type as parameter, or the name of a template variable.
- It doesn't query elements that exist within the `ng-content` tag.



### QueryList

- The return type of `@ViewChildren` is `QueryList`.
- It's an simple object which stores a list of items but Angular will automatically update the object items when the state of the application changes.
- Think of the `QueryList` as an observable collection which can emit events once items are added or removed from it. We can access the observable wrapped by the `QueryList` with its `changes` property. [Ref](https://angular.io/api/core/QueryList#changes)



## So basically...

- Any directive, component and element which is part of component template can be accessed as `@ViewChild`/`@ViewChildren`.
- `@ViewChild`/`@ViewChildren` are ~~only~~ initialised by the time the `AfterViewInit` lifecycle phase has been run. 



## References

- [Angular @ViewChild: In-Depth Explanation](https://blog.angular-university.io/angular-viewchild/)
- [Angular @ViewChild Example](https://www.concretepage.com/angular-2/angular-2-viewchild-example)
- [Understanding @ViewChildren, @ViewChild, @ContentChildren and @ContentChild](https://medium.com/@tkssharma/understanding-viewchildren-viewchild-contentchildren-and-contentchild-b16c9e0358e)