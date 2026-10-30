# Project Structure

The Ionic + Angular client on `feat/h3-pantrypal-mvp` uses a feature-first structure, with UI, domain rules, and data access separated. The structure below reflects the current implementation branch. Empty generic `core/` and `shared/` folders are intentionally omitted until a concrete shared use case requires them.

~~~text
src/app/
├── app.component.*                         Root Ionic shell
├── app.routes.ts                            Centralized Angular routes
├── home/
│   ├── home.page.ts / .html / .scss        Pantry list and search
│   └── home.page.spec.ts                  Home-page test
└── features/pantry/
    ├── domain/
    │   ├── product.ts
    │   ├── product-validation.ts
    │   └── expiration.ts
    ├── data/
    │   ├── product-api.repository.ts
    │   ├── product.dto-mapper.ts
    │   ├── api-resilience.interceptor.ts
    │   └── product-repository.token.ts
    └── presentation/
        ├── pantry.facade.ts
        ├── product-form.page.ts / .html / .scss
        ├── product.resolver.ts
        ├── pending-changes.guard.ts
        └── native-feedback.service.ts
~~~

## Rules

- One feature = one folder under features/; PantryPal's MVP is small enough to start with only pantry/.
- Domain rules must not import Angular, Ionic, HTTP, or database-specific code.
- Page components must not call the API directly; they dispatch user actions to the feature facade/use case.
- The data layer implements repository contracts and is the only mobile layer that knows HTTP DTOs.
- The app communicates with the REST API; the mobile app must not connect directly to PostgreSQL.
- Avoid introducing generic layers or separate services until there is a concrete use case.

## Naming

| Thing | Convention | Example |
|---|---|---|
| Feature folder | kebab-case | pantry/ |
| Angular component | Angular project convention | pantry-list.page.ts |
| Type / class | PascalCase | ProductRepository |
| File | Angular project convention | product.repository.ts |
