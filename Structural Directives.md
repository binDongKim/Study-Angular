# Structural Directives

## *ngIf

### *ngIf vs hidden

> *ngIf : **Adds/Removes** the element to DOM.
>
> hidden: **Shows/hides** the element in DOM.



*So, add/remove or show/hide?*

#### An excerpt from the official document:

The difference between hiding and removing doesn't matter for a simple paragraph. It does matter when the host element is attached to a resource intensive component. Such a component's behavior continues even when hidden. The component stays attached to its DOM element. It keeps listening to events. Angular keeps checking for changes that could affect data bindings. Whatever the component was doing, it keeps doing.

Although invisible, the component—and all of its descendant components—tie up resources. The performance and memory burden can be substantial, responsiveness can degrade, and the user sees nothing.

On the positive side, showing the element again is quick. The component's previous state is preserved and ready to display. The component doesn't re-initialize—an operation that could be expensive. So hiding and showing is sometimes the right thing to do.

But in the absence of a compelling reason to keep them around, your preference should be to remove DOM elements that the user can't see and recover the unused resources with a structural directive like `NgIf` .

These same considerations apply to every structural directive, whether built-in or custom. Before applying a structural directive, you might want to pause for a moment to consider the consequences of adding and removing elements and of creating and destroying components.



## References

- [Why remove rather than hide?](https://angular.io/guide/structural-directives#why-remove-rather-than-hide)