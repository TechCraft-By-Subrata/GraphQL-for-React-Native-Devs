# Lesson 1.4 - Understanding GraphQL Architecture

## Goal

Understand the moving pieces in a GraphQL app without going deep into backend development.

## The big picture

A React Native app sends a GraphQL operation to a GraphQL server.

The server checks the operation against the schema, runs resolver functions, and returns JSON.

```txt
React Native screen
  -> GraphQL query or mutation
  -> GraphQL server
  -> Schema validation
  -> Resolvers
  -> Data sources
  -> JSON response
  -> React Native UI
```

## The client

The client is your React Native app.

At the beginning of the course, we can use `fetch`.

Later, we will use Apollo Client because it gives us:

- `useQuery`
- `useMutation`
- Loading and error state helpers
- Normalized cache
- Pagination tools
- Refetching tools
- TypeScript integration

## The GraphQL endpoint

The endpoint for this course is:

```txt
https://backend.ecom.subraatakumar.com/graphql
```

Most GraphQL APIs use a single HTTP endpoint for queries and mutations.

## The schema

The schema is the contract between the app and the API.

It answers questions like:

- Which queries exist?
- Which mutations exist?
- What arguments do they accept?
- Which fields can I request?
- Which fields are required?

Example from this course:

```graphql
type Product {
  id: Int!
  name: String!
  price: Float!
  category: String!
  imageUrl: String
  description: String
  brand: String
  inStock: Boolean!
  quantity: Int
  unit: String
  discount: Int
  rating: Float
}
```

Beginner translation:

- `String` means text.
- `Int` means whole number.
- `Float` means decimal number.
- `Boolean` means true or false.
- `!` means the field should not be null.

## Queries

Queries read data.

Example:

```graphql
query ProductList {
  products(page: 1, size: 5) {
    data {
      id
      name
      price
    }
  }
}
```

Use queries for screens like:

- Product list
- Product details
- Categories
- Order history

## Mutations

Mutations change data.

Example:

```graphql
mutation SignIn($input: SignInInput!) {
  signIn(input: $input) {
    accessToken
    refreshToken
  }
}
```

Variables:

```json
{
  "input": {
    "username": "testuser",
    "password": "Test@123"
  }
}
```

Use mutations for actions like:

- Sign in
- Create order
- Cancel order
- Return order
- Logout

## Resolvers

Resolvers are backend functions that produce the data for a field.

As a React Native developer, you usually do not write resolvers. But you should understand that every field in your query has backend work behind it.

This matters because deeply nested or very large queries can be expensive.

## GraphiQL

GraphiQL is a browser-based tool for writing and testing GraphQL operations.

Use it before writing React Native code:

1. Open `https://backend.ecom.subraatakumar.com/graphiql`.
2. Write the query.
3. Run it.
4. Check the response shape.
5. Copy the working query into your app.

This workflow prevents a lot of React Native debugging noise.

## HTTP request shape

A GraphQL request is usually a `POST` request with JSON.

```json
{
  "query": "query HealthCheck { health }",
  "variables": {}
}
```

When using auth, add the token in headers:

```json
{
  "Authorization": "Bearer YOUR_ACCESS_TOKEN"
}
```

## Check your understanding

- What is the schema?
- What is the difference between a query and a mutation?
- Why should you test an operation in GraphiQL before putting it into React Native?

## External reading

- [GraphQL Learn: Schemas and Types](https://graphql.org/learn/schema/)
- [Apollo Client: Queries](https://www.apollographql.com/docs/react/data/queries)
- [Apollo Client: Mutations](https://www.apollographql.com/docs/react/data/mutations)
