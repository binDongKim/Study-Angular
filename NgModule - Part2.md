# NgModule - Part 2

## Module encapsulation

- The declarable types - `components`, `directives`, `pipes` - can only be used by the components either declared inside the module which imports the module which declares the declarables(assuming the module exports them).
- There's no encapsulation for the components added to the `entryComponents`(dynamic component) so you don't need to export them but you will still need to import the module to use the component.
- There's no encapsulation for the providers, meaning that a provider declared in any non-lazy loaded module can be accessed anywhere inside the application.



## Modules hierarchy

- All modules are merged during compilation phase, meaning that there's no hierarchical relationship between the module that is imported and the module that imports.

- Angular compiler generates a **factory** for the root module(`AppModule` in general)

  - This **factory** uses `createNgModuleFactoy` function which takes: 

    - module class reference 
    - bootstrap components
    - component factory with resolver with entry components
    - definition factory with **merged** module providers

  - When the compiler generates a module factory for the root module, it **merges** providers from all modules together and creates a factory only for this one merged module:

  - ```javascript
    createNgModuleFactory(
        // reference to the AppModule class
        AppModule,
    
        // reference to the AppComponent that is used
        // to bootstrap the application
        [AppComponent],
    
        // module definition with merged providers
        moduleDef([
            ...
    
            // reference to component factory resolver
            // with the merged entry components
            moduleProvideDef(512, jit_ComponentFactoryResolver_5, ..., [
                ComponentFactory_<BComponent>,
                ComponentFactory_<AComponent>,
                ComponentFactory_<AppComponent>
            ])
    
            // references to the merged module classes 
            // and their providers
            moduleProvideDef(512, AModule, AModule, []),
            moduleProvideDef(512, BModule, BModule, []),
            moduleProvideDef(512, AppModule, AppModule, []),
            moduleProvideDef(256, 'a', 'a', []),
            moduleProvideDef(256, 'b', 'b', []),
            moduleProvideDef(256, 'root', 'root', [])
    ]);
    ```

  - As you can see above, the providers and the entry components from all modules are merged and passed into `moduleDef` function, meaning that no matter how many modules you import only one factory with merged providers is created.



## Lazy loaded modules

- Angular creates its own injector for the lazy loaded modules and it's a child of the root injector.
- This happens because Angular generates a **separate** factory for each lazy loaded module.
- It means that providers defined in such module are not merged into the main module injector.
- So if a lazy loaded module defines a provider with the same token as in the root module, Angular will create a new instance of that service even if there's already one in the main module injector.
- So lazy loaded modules do create hierarchy but it's the hierarchy of **injectors**, not modules.



## Module caching

- All existing module loaders cache the module they load.
  - When SystemJS loads a module, it puts it in the cache.
  - Next time there's a request for this module, it returns it from cache.

