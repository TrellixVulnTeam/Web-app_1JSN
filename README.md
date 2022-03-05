# Efoye - Happy baby, Happy Family

ReacJS web UI for SNS eCommerce.  

## Installation

System requirements:

- [node v14](https://nodejs.org/download/release/v14.18.3/)
- [docker-compose](https://docs.docker.com/compose/install/)
- [dotnet core 6](https://docs.microsoft.com/en-us/dotnet/core/install/) 
- Entity Framework (after installing dotnet core)
  - `dotnet tool install --global dotnet-ef`

From the Efoye directory run:

```bash
npm install
```

## Run

```bash
npm run dev
```

## Developing Standards

- Routing

  Routing is done dynamically in the /pages directory.  See the [Nextjs routing documents](https://nextjs.org/docs/routing/introduction) for in depth routing.  We will be using index routes, and dynamic routes. **Please do NOT put business logic in the /Pages files.  Anything more than component rendering should go in the /src directory**

- /src directory

  The bulk of the development should be in this directory.  Each component or module should have its own top level directory here.  The directory structure under each component/module should be:

  ```text
  src    
      SomeModule
      components (react components)
      lib (common classes, libraries, utilities for the module)
      state (if a state object is required)
  ```

- DB access

  Until our API is up and running, we are using Prisma to directly query the DB.  Please see the [Prisma Docs](https://www.prisma.io/docs/concepts/components/prisma-client) for details on using the client.  Please see:

  - [./prisma/schema.prisma](./prisma/schema.prisma) - for the database schema
  - [./prisma/seedDev.ts](./prisma/seedDev.ts) - for the initial seeding file.  This file will contain examples of how to read, write, and connect relations.

We want to keep the website as static as possible.  Use `getStaticPaths` and `getStaticProps` as much as possible and "rehydrate" the data on the client side.  Ths will allow the website to be pushed to the edge as much as possible.  

## Database Schema Updates

The web api is not up and running yet so we are directly connecting to the database via prisma.  The database schema's "source of truth" is still in the SNSBackend's code.  To make schema changes:

1. Update the model in SNSBackend/Model
2. Add a new migration with entity framework

    ```bash
    dotnet ef migrations add <short description>
    ```

3. Update the database

    ```bash
    dotnet ef database update
    ```

4. Pull database into prisma and update the client

   ```bash
    npx prisma db pull && npx prisma generate
   ```

## Branch Structure

The production branch is the "main" branch.  Only pull requests from master will be allowed to change the main branch.

Developers should create branches for each feature/bug.  Once the unit of work it complete, create a pull request into master.  Squash merge the pull requests into master.

## CI/CD

Any commit into master or main branch with kick off a "GitHub Action" to deploy to Vercel.  The actions will:

1. Update the database with latest migration.  Revert the migration to test rollback.  Re-apply the latest migration

2. Seed the database

3. Deploy to Vercel.  Check the action log to view the deployed site.
