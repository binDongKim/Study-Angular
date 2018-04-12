# Angular Templates

- ng-template
- ng-container
- ngTemplateOutlet



## ng-template

### ng-template and ngIf

```Html
<div class="lessons-list" *ngIf="lessons else loading">
  ... 
</div>

<ng-template #loading>
    <div>Loading...</div>
</ng-template>

```

The sugar syntax `*ngIf` use **ng-template** under the hood:

```html
<ng-template [ngIf]="lessons" [ngIfElse]="loading">
   <div class="lessons-list">
     ... 
   </div>
</ng-template>

<ng-template #loading>
    <div>Loading...</div>
</ng-template>
```



## ng-container

We can't have multiple template bindings on one element:

```html
<div class="lesson" *ngIf="lessons" *ngFor="let lesson of lessons">
    <div class="lesson-detail">
        {{lesson | json}}
    </div>
</div>  

```

The above example would throw an error so to make it work, we would need to create an extra `div` element to achieve this, like:

```html
<div *ngIf="lessons">
    <div class="lesson" *ngFor="let lesson of lessons">
        <div class="lesson-detail">
            {{lesson | json}}
        </div>
    </div>
</div>
```

This works but we can use **ng-container** in this matter:

```html
<ng-container *ngIf="lessons">
    <div class="lesson" *ngFor="let lesson of lessons">
        <div class="lesson-detail">
            {{lesson | json}}
        </div>
    </div>
</ng-container>
```

**ng-container** inserts the inner elements without creating any extra element in DOM. 

*Note: Another major use case for **ng-container** is that we can use it as a placeholder to inject a template dynamically into the page*



## ngTemplateOutlet

We can take the template itself and instantiate it anywhere on the page by using the **ngTemplateOutlet** directive:

```html
<ng-container *ngTemplateOutlet="loading"></ng-container>
```

Like the above example, we can instantiate the `loading` template we defined somewhere.

We refer the toe `loading` template via its template reference `#loading` and access it like above with **ngTemplateOutlet**.



### Template Context

Each template can also define its own set of input variables. More precisely, each template has associated a context object containing all the template-specific input variables.

```javascript
@Component({
  selector: 'app-root',
  template: `      
    <ng-template #estimateTemplate let-lessonsCounter="estimate">
    	<div> Approximately {{lessonsCounter}} lessons ...</div>
    </ng-template>
    <ng-container *ngTemplateOutlet="estimateTemplate;context:ctx"></ng-container>
`})
export class AppComponent {
    totalEstimate = 10;
    ctx = {estimate: this.totalEstimate};
  
}
```

- This time, the template has one input variable: `lessonCounter` and it's definied via **ng-template** property using the prefix `let-`.
- `lessonCounter` is only visible inside the **ng-template** body, but not the outside.
- The content of this variable is determined by the expression assigned to the property `let-lessonCounter` and that expression is evaluated upon a *context object* which is passed to **ngTemplateOutlet** via `context` property with the template to instantiate.
- This *context object* must then have a property named `estimate` for any value to be displayed inside the template.



## Template References

We can inject a template directly into our component using `ViewChild` decorator with `template reference`:

```javascript
@Component({
  selector: 'app-root',
  template: `      
      <ng-template #defaultTabButtons>
          <button class="tab-button" (click)="login()">
            {{loginText}}
          </button>
          <button class="tab-button" (click)="signUp()">
            {{signUpText}}
          </button>
      </ng-template>
`})
export class AppComponent implements OnInit {
    @ViewChild('defaultTabButtons')
    private defaultTabButtonsTpl: TemplateRef<any>;

    ngOnInit() {
        console.log(this.defaultTabButtonsTpl);
    }
}
```

As you can see above, the templates are accessible at the level of the component and with this approach, we could do things such as pass a template to child components, which enables us to create a more customizable component. 



## Configurable Components with Template Partial @Inputs 

Let's pass a template as an input parameter:

```javascript
@Component({
  selector: 'app-root',
  template: `      
	<ng-template #customTabButtons>
        <div class="custom-class">
            <button class="tab-button" (click)="login()">
              {{loginText}}
            </button>
            <button class="tab-button" (click)="signUp()">
              {{signUpText}}
            </button>
        </div>
	</ng-template>
	<tab-container [headerTemplate]="customTabButtons"></tab-container>      
`})
export class AppComponent implements OnInit {}
```

```javascript
@Component({
    selector: 'tab-container',
    template: `
		<ng-template #defaultTabButtons>
            <div class="default-tab-buttons">
                ...
            </div>
		</ng-template>
		<ng-container
			*ngTemplateOutlet="headerTemplate ? headerTemplate: defaultTabButtons">
   		</ng-container>
`})
export class TabContainerComponent {
    @Input() headerTemplate: TemplateRef<any>;
}
```

- There's a default template defined for the tab buttons: `defaultTabButtons`
- `defaultTabButtons` will be used only if the input property `headerTemplate` remains undefined.
- If the property is defined, the custom input template passed via `headerTemplate` will be used.



## References

- [The Complete Guide to Angular Templates](https://blog.angular-university.io/angular-ng-template-ng-container-ngtemplateoutlet/)

