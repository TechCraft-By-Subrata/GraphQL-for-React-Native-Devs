# Lesson 1.1 - REST vs GraphQL

## Goal

Understand the difference between REST and GraphQL from the point of view of a React Native developer building screens.

## What is REST?

REST is an API style where the backend exposes multiple URLs, usually called endpoints.

Examples from the e-commerce backend:

```txt
GET https://backend.ecom.subraatakumar.com/api/v1/products?page=1&size=10
GET https://backend.ecom.subraatakumar.com/api/v1/categories
```

In REST, the backend decides the response shape for each endpoint. Your React Native screen receives whatever fields the endpoint returns.

That can be perfectly fine for many apps. REST is simple, familiar, and easy to test in a browser.

## What is GraphQL?

GraphQL is a query language for APIs. Instead of calling many fixed endpoints, the app usually sends a query to one endpoint and asks for the exact fields it needs.

GraphQL endpoint used in this course:

```txt
POST https://backend.ecom.subraatakumar.com/graphql
```

Example GraphQL query:

```graphql
query ProductList {
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

This query says: "For this screen, I only need product id, name, price, and imageUrl."

## The main difference

REST is endpoint-first.

GraphQL is screen-data-first.

In REST, you usually ask:

```txt
Which endpoint gives me the data?
```

In GraphQL, you usually ask:

```txt
What fields does this screen need?
```

## REST request example

```ts
async function fetchProductsWithRest() {
  const response = await fetch(
    "https://backend.ecom.subraatakumar.com/api/v1/products?page=1&size=10"
  );

  if (!response.ok) {
    throw new Error("Failed to load products");
  }

  return response.json();
}
```

## GraphQL request example

```ts
async function fetchProductsWithGraphQL() {
  const response = await fetch("https://backend.ecom.subraatakumar.com/graphql", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      query: `
        query ProductList {
          products(page: 1, size: 10) {
            data {
              id
              name
              price
              imageUrl
            }
          }
        }
      `,
    }),
  });

  const result = await response.json();

  if (result.errors) {
    throw new Error(result.errors[0].message);
  }

  return result.data.products.data;
}
```

## What React Native developers should remember

For mobile apps, the question is not "Is REST bad?"

The better question is:

```txt
Can my screen request only the data it needs, in the fewest number of network calls?
```

GraphQL is designed to make that easier.

## Check your understanding

- In REST, who controls the response shape?
- In GraphQL, who chooses the fields returned for a screen?
- Why can this matter more on mobile than on desktop web?

## External reading

- [GraphQL Learn: Queries](https://graphql.org/learn/queries/)
- [GraphQL Learn: Serving over HTTP](https://graphql.org/learn/serving-over-http/)
