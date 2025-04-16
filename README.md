# 📦 Prisma Query

> 🛠 Forked from [chax-at/prisma-filter](https://github.com/chax-at/prisma-filter)

This library helps you easily create dynamic `filter` queries for Prisma. It supports building safe and flexible `where` conditions using TypeScript, and is especially useful when combined with frameworks like NestJS.

👉 **Original Documentation:** [https://github.com/chax-at/prisma-filter](https://github.com/chax-at/prisma-filter)

---

### Install - Backend

With Yarn:

```bash
yarn add prisma-filter
```

With Npm:

```bash
npm i prisma-filter
```

### Install - Frontend

With Yarn:

```bash
yarn add prisma-filter-common
```

With Npm:

```bash
npm i prisma-filter-common
```

### Building a filter

This package provides a `FilterBuilder<T>` class which can be used to create the filter:

```typescript
import { FilterBuilder, FilterOperationType } from "prisma-querystring";

const filterBuilder = new FilterBuilder<User>() // create a new filter builder for User entities..
  .addFilter("name", FilterOperationType.Ilike, "%Max%") // ...filter by name ilike '%Max%'
  .addOrFilter("email", FilterOperationType.Ilike, "%email%") // ...orFilter by email ilike '%email%'
  .addOrderBy("name", "asc") // ...order by name, asc
  .setPageSize(40) // ...paginate with a pagesize of 40
  .requestPage(3); // ...return the third page
const filter = filterBuilder.toFilter(); // get the resulting IFilter<User>
const queryString = filterBuilder.toQueryString(); // get the resulting query string (as described below)

// Note that you can also re-use the same filter if you just want to request a different page without changing filter or ordering:
const firstPageFilter = filterBuilder.requestPage(1).toFilter();
```

#### Building a query string

In the end, a query string is required which will be sent to the backend server. To build this query string,
you can use `FilterBuilder.toQueryString()` when building a filter using the FilterBuilder as described above.
However, it is also possible to transform an existing filter into a query string:

```typescript
const queryString = FilterBuilder.buildFilterQueryString({
  limit: 20,
  offset: 30,
  filter: [
    { field: "field1", type: FilterOperationType.NeNull, value: "val1" },
  ],
  orFilter: [
    { field: "field2", type: FilterOperationType.NeNull, value: "val2" },
  ],
  order: [{ field: "field1", dir: "asc" }],
});
// queryString is
// ?offset=30&limit=20&filter[0][field]=field1&filter[0][type]=nenull&filter[0][value]=val1&orFilter[0][field]=field2&orFilter[0][type]=nenull&orFilter[0][value]=val2&order[0][field]=field1&order[0][dir]=asc&order[1][field]=field2&order[1][dir]=desc
```
