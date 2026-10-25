# Project Structure

Organize the Ionic + Angular client by **feature**, with UI, domain rules, and data access separated. The following is the target structure for PantryPal; it describes the intended organization and does not claim these feature files already exist in the repository.

~~~text
src/app/
├── core/
│   ├── api/                 API base URL and shared HTTP configuration
│   ├── routing/             Shared route configuration if not at app root
│   └── theme/               Global theme tokens and styles
├── features/
│   └── pantry/
│       ├── presentation/    Pantry list, product form, alert/dialog, facade/state
│       ├── domain/          Product entity, repository contract, validation,
│       │                    expiration rules, and use cases
│       └── data/            HTTP data source, DTOs, mapper, repository implementation
├── shared/                  Reusable Ionic UI pieces (no pantry business rules)
├── app.component.*          Root Ionic shell and router outlet
└── app.routes.ts             Single centralized Angular route table
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
