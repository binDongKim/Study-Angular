# Angular Dependency Injection

## Injector

- An Angular Injector is responsible for creating service instances and injecting them into classes which seem to want to use them.
- Angular creates injectors for you as it excutes the app, starting with the root injector created during the bootstrap process.
- Angular doesn't automatically know how you want Injector to create instances of the services. You must configure it by specifying **providers** for every service.
- **Providers** tell the injector how to create the service.



## @Injectable Providers - Preferred way!!

- The **@Injectable** decorator assures Angular that the class with this decorator can have dependencies. In other words, Angular(Injector) can inject things(services) into the class.

- It can also be used to configure a provider:

  ```javascript
  import { Injectable } from '@angular/core';
  
  @Injectable({
  	providedIn: 'root',
  })
  export class HeroService {
  	constructor() { }
  }
  ```

  `provideIn` tells Angular that the root injector is responsible for creating an instance of `HeroService` and making it available across the application.

- `provideIn` can also be associated with `@NgModule` class level:

  ```javascript
  import { Injectable } from '@angular/core';
  import { HeroModule } from './hero.module';
  import { HEROES }     from './mock-heroes';
  
  @Injectable({
  	providedIn: HeroModule,
  })
  export class HeroService {
  	getHeroes() { return HEROES; }
  }
  ```

  We declare that this service should be created by any injector that includes HeroModule. In other words, `HeroService` will only be available to applications if they import `HeroModule`.



### @NgModule Providers

You can register services in `@NgModule` as following:

```javascript
import { NgModule } from '@angular/core';
import { AppComponent } from './app.component';
 
@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [],
  providers: [SomeService],
  bootstrap: [AppComponent]
})
export class AppModule { }
```



### @Component Providers

- Services can also be provided in speicifc components.
- Services provided in component-level is only available within that component injector or in any of its child components.
- Every component instance has its own injector. Providing the service at the component level ensures that every instance of the component gets its own, private instance of the service.



### @Injectable, @NgModule or @Component?

*Where should we provide a service?* 

The choices lead to differences in the final bundle size, service scope and service lifetime.



**Case 1.** Registering providers in the **@Injectable** decorator of the service itself:

- Optimization tools can perform tree shaking, which removes services that aren't used by your app => Smaller bundle sizes.

**Case 2.** Angular **module** providers(`@NgModule.providers`):

-  It's registered with the application's root injector unless the module is *lazy loaded*.
-  Angular can inject the corresponding services in any class it creates.
-  Once created, a service instance lives for the life of the app and Angular injects this one service instance in every class that needs it(Singleton).
-  When `providers` of `@NgModule()` decorator configures a service in application module then that service will be available for dependency injection in all those components which are configured in `declarations` metadata of that `@NgModule()` decorator. 

**Case 3.** Component's providers(`@Component.providers`):

- It's registered with each component instance's own injector.
- Angular can only inject the corresponding services in that component instance or one of its descendant component instance.
- Each new instance of the component gets its own instance of the service and, when the component instance is destroyed, so is that service instance.



## Providers

- The injector relies on `providers` to create instances of the services that the injector injects into components, directives, pipes and other services.
- You must register a service provider, or the injector won't know how to create the service.
- `providers` has two parts: **Token** and **Provider Definition Object**.
  - **Token** is configured by `provide` attribute.
  - **Provider Definition Object** is configured by `useClass`, `useExisting`, `useValue`, `useFactory`.



### The Various Forms of Providers

#### Class Providers

```javascript
providers: [Logger]
```

is a shorthand expression for:

```javascript
providers: [
    { provide: Logger, useClass: Logger }
]
```

The `provide` property holds the *token* that serves as the key for both locating a dependency value and registering the provider.



```javascript
providers: [
    { provide: Logger, useClass: BetterLogger }
]
```

You may want to ask a different class to provide the service. For example, the above code tells the injector to return a `BetterLogger` instance when something asks for the `Logger`. 

*Note*: The class type of provider definition object must be same as token class or its subclass.



#### Handling The Alias Class Providers

```javascript
providers: [
	NewLogger,
	{ provide: OldLogger, useClass: NewLogger}
]
```

The injector will create two instances of `NewLogger` instead of using/injecting the singleton instance as you intend.

**The solution:** alias with the `useExisting` option.

```javascript
providers: [
	NewLogger,
	{ provide: OldLogger, useExisting: NewLogger}
]
```



#### Value Providers

```javascript
// An object in the shape of the logger service
export function SilentLoggerFn() {}

const silentLogger = {
  logs: ['Silent logger says "Shhhhh!". Provided via "useValue"'],
  log: SilentLoggerFn
};
```

Then you register a provider with the `useValue` option, which makes this object play the logger role:

```javascript
providers: [
	{ provide: Logger, useValue: silentLogger }
]
```



#### Factory Providers

```javascript
// src/app/heroes/hero.service.ts
@Injectable
constructor(
  private logger: Logger,
  private isAuthorized: boolean
) { }

getHeroes() {
  let auth = this.isAuthorized ? 'authorized ' : 'unauthorized';
  this.logger.log(`Getting heroes for ${auth} user.`);
  return HEROES.filter(hero => this.isAuthorized || !hero.isSecret);
}
```

You can inject the `Logger` but you can't inject the boolean `isAuthorized` . We need a factory provider at this moment:

```javascript
// src/app/heroes/hero.service.provider.ts
let heroServiceFactory = (logger: Logger, userService: UserService) => {
  return new HeroService(logger, userService.user.isAuthorized);
};

export let heroServiceProvider =
  { 
    provide: HeroService,
    useFactory: heroServiceFactory,
    deps: [Logger, UserService]
  };
```

The `useFactory` field tells Angular that the provider is a factory function whose implementation is the `heroServiceFactory`. The `deps` property is an array of *provider tokens*. The `Logger` and `UserService` classes serve as tokens for their own class providers. The injector resolves these tokens and injects the corresponding services into the match factory function parameters.



### Tree-shakable Providers

To make services tree-shakable, the information that used to be specified in the module(`@NgModule.provider`) should be specified in the `@Injectable` decorator on the service itself.

```javascript
@Injectable({
  providedIn: 'root',
})
export class Service {
}
```

In the example above, `providedIn` allows you to declare the injector which injects this service. Unless there's a special case, the value should always be root. Setting the value to root ensures that the service is scoped to the root injector, without naming a particular module that is present in that injector.

```javascript
@Injectable({
  providedIn: 'root',
  useFactory: () => new Service('dependency'),
})
export class Service {
  constructor(private dep: string) {
  }
}
```

The service can be instantiated by configuring a factory function as above.



## Singleton Services

- Services are singletons within the scope of an Injector, which means there's at most one instance of a service in a given injector.
- There's only one root injector and ,for example, let's say the `UserService` is registered with that injector. Therefore, there can be just one `UserService` instance in the entire app and every class gets this service instance.
- However, Angular DI is a hierarchical injection system, which means that nested injectors can create their own service instances.



## Component Child Injectors

- Component injectors are independent of each other and each of them creates its own instances of the *component-provided services*.
- When Angular creates a new instance of a component that has `@Component.providers`, it also creates a *new child injector* for that instance. When Angular destorys one of these component instances, it also destroys the component's injector and that injector's service instances.
- Because of *injector inheritance*, a component's injector is a child of its parent component's injector and a descendent of its parent's parent's injector and so on all the way back to the application's *root* injector. Angular can inject a service provided by any injector in that lineage.



## Dependency Injection Tokens

- When you register a provider with an injector, you associate the provider with a dependency injection token.
- The injector maintains an internal *token-provider* map that it references when asked for a dependency.
- The token is the key to the map.



### Class Dependencies

```javascript
providers: [
    { provide: Logger, useClass: Logger }
]
```

The class type served as the token(key).



### Non-Class Dependencies

There's some cases that you want to inject a string, function or object.

For example:

```javascript
export const HERO_DI_CONFIG: AppConfig = {
  apiEndpoint: 'api.heroes.com',
  title: 'Dependency Injection'
};
```

If you'd like to make this configuration object available for injection, you can register it with a *value provider* but what should you use as the *token*? There is no `AppConfig` class.



**Important Note**: TypeScript interfaces aren't valid tokens!

*Why?* 

It's because an interface is a TypeScript design-time artifact. Javascript doesn't have interfaces. The TypeScript interface disappears from the generated Javascript. There's no interface type information left for Angular to find at runtime.



#### InjectionToken

The solution for setting up a provider token for non-class dependencies is to define and use an `InjectionToken`:

```javascript
import { InjectionToken } from '@angular/core';

// The first parameter of InjectionToken is just to describe about the value.
export const TOKEN = new InjectionToken('desc');

// ------- //

{ provide: TOKEN, useValue: 'Description!' }
```

Or you can directly configure a provider when creating an `InjectionToken`:

```javascript
export const TOKEN = 
  new InjectionToken('desc', { providedIn: 'root', factory: () => new AppConfig(), })
```

The provider configuration determines which injector provides the token and how the value will be created.

And now you can inject the configuration object into any constructor that needs it:

```javascript
constructor(@Inject(TOKEN));
```



If the factory function needs access to other DI tokens, it can use the `inject` function from `@angular/core` to request dependencies:

```javascript
const TOKEN = 
  new InjectionToken('tree-shakeable token', 
    { providedIn: 'root', factory: () => 
        new AppConfig(inject(Parameter1), inject(Paremeter2)), });
```



## Optional Dependencies

You can tell Angular that the dependency is optional by annotating the constructor argument with null:

```javascript
constructor(@Inject(Token, null));
```



## Hierarchical Dependency Injectors

> Angular has a *Hierarchical Dependency Injection* system. There's a tree of injectors that parallel an application's component tree.



### The Injector Tree

- An Angular application is a tree of components and each component instance has its own injector so, the tree of components parallels the tree of injectors.
- The component's injector may be a proxy for an ancestor injector higher in the component tree.



#### Injector Bubbling

- When a component requests a dependency, Angular tries to satisfy that dependency with a provider registered in that component's own injector.
- If the component's injector lacks the provider, it passes the request up to its parent component's injector.
- If that injector can't satisfy the request, it passes it along to its parent injector.
- The requests keep bubbling up until Angular finds an injector that can handle the request .
- If it can't be resolved on the root injector, Angular throws an error.



#### Re-providing a service at different levels

You can re-register a provider for a particular dependency token at multiple levels of the injector tree.



### Control Dependency Lookup with *@Optional()* and *@Host()*

When a component requests a dependency, Angular starts with that component's injector and walks up the injector tree until it finds the first suitable provider. Angular throws an error if it can't find the dependency during that walk.

**But**, you can modify/control Angular's search behaviour with the `@Host` and `@Optional` decorators.

- `@Optional` decorator tells Angular to continue even if it can't find the dependency. Angular sets the injection parameter to `null` instead.
- `@Host` decorator stops the upward search at the *host* component. The *host* component is typically the component requesting the dependency. But when this component is projected into a *parent* component, that parent component becomes the host.

Example:

```javascript
@Component({
    selector: 'app-hero-contact',
    template: `
        <div>Phone #: {{phoneNumber}}
        <span *ngIf="hasLogger">!!!</span></div>
	`
})
export class HeroContactComponent {
    hasLogger = false;
	constructor(
        @Host()
        private heroCache: HeroCacheService,
 
        @Host()     
		@Optional() // ok if the logger doesn't exist
      	private loggerService: LoggerService
  	) {
        if (loggerService) {
        	this.hasLogger = true;
        	loggerService.logInfo('HeroContactComponent can log!');
        }
	}
}
```











## References

- [Dependency Injection - Angular Official Document](https://angular.io/guide/dependency-injection)