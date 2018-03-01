# Angular Change Detection Strategy

## Default vs OnPush

- Default
  - Angular has to be conservative and run change detection every single time for every component when an event happens.
- OnPush
  - This will inform Angular that our component only depends on its inputs and that any object that is passed to it should be considered immutable.
  - Doing this will instruct Angular to run change detection on these components and their sub-tree only when new references are passed to them.
  - When using OnPush detectors, then the framework will check an OnPush component when any of its input properties changes, when it fires an event, or when an Observable fires an event.
  - You probably know that Angular tracks binding inputs by object reference. It means that if an object reference hasn’t changed the binding change is not detected and change detection is not executed for a component that uses OnPush strategy.

## markForCheck vs detactChanges

- markForCheck

  - It simply goes upwards from the current component to the root component and updates their view state to `ChecksEnabled`.
  - The actual change detection for the component is not scheduled but when it will happen in the future (either as part of the current or next CD cycle) the parent component views will be checked even if they had detached change detectors.

- detactChanges

  - This one is used to run change detection for the tree of components starting with the component that you trigger `detectChanges()` on.
  - So the change detection will run for the current component and all its children.

- You should use `markForCheck` when you either inside the change detection process already, like in `ngDoCheck` lifecycle hook, or when you expect a change detection cycle to run in the immediate future. If in these cases you used `detectChanges`, you would have one redundant change detection run.

- If you know that after the property change the change detection will run, for example, you changed the property in `setTimeout`, then use `markForCheck`. If the property changed outside of Angular and the change detection cycle is not scheduled, use `detectChanges`

  ​

## LifeCycleHook and ChangeDetection

- ngOnChanges is only triggered when the __input binding property__ changes. When it comes to object and array which are mutable, the hook is only triggered when the reference of the property changes no matter the change detection strategy is default or OnPush.

### General

- When an asynchronous event takes place, Angular triggers change detection on its top-most ViewRef, which after running change detection for itself **runs change detection for its child views**.