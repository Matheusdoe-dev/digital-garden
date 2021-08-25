# Modules

- A module is a class annotated with a `@Module()` decorator. The `@Module()` decorator provides metadata that **Nest** makes use of to organize the application structure.

![Application Module](https://docs.nestjs.com/assets/Modules_1.png)

- Each application has at least one module, a **root module**. The root module is the starting point Nest uses to build the **application graph**.

- The **application graph** is the internal data structure Nest uses to resolve module and provider relationships and dependencies.

- Modules are very strongly recommended as an effective way to organize components. Thus, for most applications, the resulting architecture will employ multiple modules, each encapsulating a closely related set of **capabilities**.

- The `@Module` decorator takes a single object whose properties describe the module:

|             |                                                                                                                              |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------- |
| providers   | the providers that will be instantiated by the Nest injector and that may be shared at least across this module              |
| controllers | the set of controllers defined in this module which have to be instantiated                                                  |
| imports     | the list of imported modules that export the providers which are required in this module                                     |
| exports     | the subset of `providers` that are provided by this module and should be available in other modules which import this module |

## Feature modules

- A feature module simply organizes code relevant for a specific feature, keeping code organized and establishing clear boundaries

- HINT - To create a module using the CLI, simply execute the `$ nest g module cats` command.

## Shared modules

- In Nest, modules are **singletons** by default, and thus you can share the same instance of any provider between multiple modules effortlessly.

![Shared Modules](https://docs.nestjs.com/assets/Shared_Module_1.png)

- Every module is automatically a **shared module**. Once created it can be reused by any module.

## Module re-exporting

```ts
@Module({
  imports: [CommonModule],
  exports: [CommonModule],
})
export class CoreModule {}
```

## Dependency injection

- A module class can **inject** providers as well

```ts
import { Module } from "@nestjs/common";
import { CatsController } from "./cats.controller";
import { CatsService } from "./cats.service";

@Module({
  controllers: [CatsController],
  providers: [CatsService],
})
export class CatsModule {
  constructor(private catsService: CatsService) {}
}
```

## Global modules

- When you want to provide a set of providers which should be available everywhere out-of-the-box, make the module **global** with the `@Global()` decorator.

```ts
import { Module, Global } from "@nestjs/common";
import { CatsController } from "./cats.controller";
import { CatsService } from "./cats.service";

@Global()
@Module({
  controllers: [CatsController],
  providers: [CatsService],
  exports: [CatsService],
})
export class CatsModule {}
```

- The `@Global()` decorator makes the module global-scoped. Global modules should be registered **only once**, generally by the root or core module.

## References

- [Nest.js Documentation - Modules](https://docs.nestjs.com/modules)
