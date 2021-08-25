# Providers

- The main idea of a provider is that it can be **injected** as dependency; this means objects can create various relationships with each other, and the function of "wiring up" instances of objects can largely be delegated to the Nest runtime system.

- Controllers should handle HTTP requests and delegate more complex tasks to **providers**.

![Nest.js docs, Providers](https://docs.nestjs.com/assets/Components_1.png)

## Services

```ts
import { Injectable } from "@nestjs/common";
import { Cat } from "./interfaces/cat.interface";

@Injectable()
export class CatsService {
  private readonly cats: Cat[] = [];

  create(cat: Cat) {
    this.cats.push(cat);
  }

  findAll(): Cat[] {
    return this.cats;
  }
}
```

- HINT - To create a service using the CLI, execute `$ nest g service <servicename>` command.

- The `@Injectable()` decorator attaches metadata, which declares that `CatsService` is a class that can be managed by the Nest IoC container.

```ts
import { Controller, Get, Post, Body } from "@nestjs/common";
import { CreateCatDto } from "./dto/create-cat.dto";
import { CatsService } from "./cats.service";
import { Cat } from "./interfaces/cat.interface";

@Controller("cats")
export class CatsController {
  constructor(private catsService: CatsService) {}

  @Post()
  async create(@Body() createCatDto: CreateCatDto) {
    this.catsService.create(createCatDto);
  }

  @Get()
  async findAll(): Promise<Cat[]> {
    return this.catsService.findAll();
  }
}
```

- The `CatsService`is **injected** through the class contructor.

## Dependency Injection

- Nest is built around the strong design pattern commonly known as **Dependency Injection**.

- Dependencies are services or objects that a class needs to perform its function. Dependency Injection, or DI, is a design pattern in which a class requests dependencies from external sources rather than creating them.

- In Nest, thanks to TypeScript capabilities, it's extremely easy to manage dependencies because they are resolved just by type. In the example below, Nest will resolve the `catsService` by creating and returning an instance of `CatsService` (or, in the normal case of a singleton, returning the existing instance if it has already been requested elsewhere).

```ts
constructor(private catsService: CatsService) {}
```

## Scopes

- Providers normally have a lifetime ("scope") synchronized with the application lifecycle. When the application is bootstrapped, every dependency must be resolved, and therefore every provider has to be instantiated. Similarly, when the application shuts down, each provider will be destroyed

## Optional providers

- Occasionally, you might have dependencies which do not necessarily have to be resolved. For instance, your class may depend ona **configuration object**, but if none is passed, the default values should be used.

- To indicate a provider is optional, use the `@Optional()` decorator in the constructor's signature.

```ts
import { Injectable, Optional, Inject } from "@nestjs/common";

@Injectable()
export class HttpService<T> {
  constructor(@Optional() @Inject("HTTP_OPTIONS") private httpClient: T) {}
}
```

## Property-based injection

```ts
import { Injectable, Inject } from "@nestjs/common";

@Injectable()
export class HttpService<T> {
  @Inject("HTTP_OPTIONS")
  private readonly httpClient: T;
}
```

## Provider registration

```ts
import { Module } from "@nestjs/common";
import { CatsController } from "./cats/cats.controller";
import { CatsService } from "./cats/cats.service";

@Module({
  controllers: [CatsController],
  providers: [CatsService],
})
export class AppModule {}
```

## References

- [Nest.js Documentation - Providers](https://docs.nestjs.com/providers)
- [Angular Documentation - Dependency Injection](https://angular.io/guide/dependency-injection)
