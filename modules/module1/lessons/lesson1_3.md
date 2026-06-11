# Lesson 1.3 - How GraphQL Solves Mobile API Problems

## Goal

See how GraphQL helps React Native screens request exactly the data they need.

## GraphQL solves over-fetching

With GraphQL, the screen lists the fields it wants.

For a compact product card:

```graphql
query ProductCards {
  products(page: 1, size: 10) {
    data {
      id
      name
      price
      imageUrl
    }
  }
}
```

For a details screen:

```graphql
query ProductDetailsList {
  products(page: 1, size: 10) {
    data {
      id
      name
      price
      imageUrl
      description
      brand
      rating
      discount
      inStock
    }
  }
}
```

Same endpoint. Different data shape.

The screen is not forced to accept every field the backend can provide.

## GraphQL reduces under-fetching

A GraphQL query can ask for related parts of the schema in one request.

Example home screen query:

```graphql
query HomeScreenData {
  categories
  products(page: 1, size: 10) {
    meta {
      total
      page
      pages
    }
    data {
      id
      name
      price
      imageUrl
      category
    }
  }
}
```

This gives the screen category chips and product cards in one GraphQL operation.

## GraphQL improves screen ownership

In React Native, each screen can own its own query.

Product list screen query:

```graphql
query ProductListScreen($page: Int!, $size: Int!, $category: String) {
  products(page: $page, size: $size, category: $category) {
    meta {
      total
      page
      pages
    }
    data {
      id
      name
      price
      imageUrl
      inStock
    }
  }
}
```

Variables:

```json
{
  "page": 1,
  "size": 10,
  "category": "fruits"
}
```

The query stays stable, and the variables change based on user actions.

## GraphQL response shape matches the query

GraphQL responses mirror the query structure.

Query:

```graphql
query CategoriesAndProducts {
  categories
  products(page: 1, size: 2) {
    data {
      id
      name
    }
  }
}
```

Response shape:

```json
{
  "data": {
    "categories": ["Fruits", "Vegetables"],
    "products": {
      "data": [
        { "id": 1, "name": "Apple" },
        { "id": 2, "name": "Tomato" }
      ]
    }
  }
}
```

The exact values may differ because the backend generates product data, but the shape is the important part.

## Running the query in GraphiQL

1. Open `https://backend.ecom.subraatakumar.com/graphiql`.
2. Paste this query:

```graphql
query HomeScreenData {
  categories
  products(page: 1, size: 5) {
    meta {
      total
      page
      pages
    }
    data {
      id
      name
      price
      imageUrl
      category
    }
  }
}
```

3. Press the play button.
4. Compare the response with the fields requested in the query.
5. Remove `imageUrl` from the query and run it again.
6. Add `description` and `rating`, then run it again.

This is the core GraphQL idea: the client controls the selection set.

## React Native fetch example

```tsx
async function loadHomeScreenData() {
  const response = await fetch("https://backend.ecom.subraatakumar.com/graphql", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      query: `
        query HomeScreenData {
          categories
          products(page: 1, size: 5) {
            meta {
              total
              page
              pages
            }
            data {
              id
              name
              price
              imageUrl
              category
            }
          }
        }
      `,
    }),
  });

  const json = await response.json();

  if (json.errors) {
    throw new Error(json.errors[0].message);
  }

  return json.data;
}
```

## Important beginner note

GraphQL is not automatically faster.

GraphQL gives the app better control over the requested data. You still need good queries, caching, pagination, and error handling. Those are the skills this course will build step by step.

## Check your understanding

- Why does the GraphQL response shape feel predictable?
- How do variables make a query reusable?
- Why is "one endpoint" not the most important benefit of GraphQL?
