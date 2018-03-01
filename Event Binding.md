# Event Binding in Angular

## Syntax

```javascript
<button (click)="onSave()">Save</button>
```

- (eventName)="callbackFunction()"

```javascript
<button (click)="onSave($event)">Save</button>
```

- The binding conveys information about the event, including data values, through an event object named *$event*.

- The shape of the event object is determined by the *target event*.

  -  If the target event is a native DOM element event, then `$event` is a DOM event object, with properties such as `target` and `target.value`.

  - If the target event is a cusom event from `EventEmitter`,  `$event` just conveys the payload `EventEmitter` sents.

    ```javascript
    <custom-component (customEvent)="callback($event)"></custom-component>

    Class CustomComponent implements OnInit {
    	customEvent = new EventEmitter<number>();
        ngOnInit() {
        	this.customEvent.emit(100);
        }
    }

    Class AppComponent {
        callback(payload: number) {
            // Output: 100
            console.log(payload);
        }
    }
    ```

