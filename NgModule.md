# NgModule

- An `NgModule` describes how the application parts fit together.
- Every application has at least one Angular module, the *root* module that you bootstrap/load to launch the application. By convention, it's usually called `AppModule`.



The default `AppModule` looks like following:

```javascript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
 
import { AppComponent } from './app.component';
 
@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```



## declarations 

- It tells Angular which components/directives/pipes belong to this module.
- You must declare every component in exactly **ONE** `NgModule` class.
- The declared classes are visible within the module but invisible to the components in a different module.
- When you need the declared classes elsewhere, export them from the module and import the module in the other module which needs them.



## imports

- It tells Angular about other `NgModule` classes that this particular module needs to function properly.
- A component template can reference another component, directive or pipe when the referenced class is declared in this module or the class was **imported** from another module.



## providers

- You list the `services` the app needs. (I covered it in Dependency Injection docs)



## bootstrap

- The application launches by bootstrapping/loading the root `AppModule`. 
- The bootstrapping process creates the component(s) listed in the `bootstrap` array and inserts each one into the browser DOM.
- Each bootstrapped component is the base of its own tree of components.
- The base/root component is usually called `AppComponent`.





## References

- [Bootstrapping](https://angular.io/guide/bootstrapping)