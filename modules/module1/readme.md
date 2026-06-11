# Module 1 - Why GraphQL for Mobile Apps?

This module explains why GraphQL is useful for React Native apps before we start writing Apollo Client code.

The goal is simple: understand the problems mobile apps face with REST APIs, then see how GraphQL changes the way a screen asks for data.

## What you will learn

- What REST is
- What GraphQL is
- Why mobile apps suffer from over-fetching and under-fetching
- Why multiple API calls are painful on real devices
- How GraphQL lets a screen request exactly the data it needs
- What the basic GraphQL architecture looks like

## Live API used in this module

- REST products endpoint: `https://backend.ecom.subraatakumar.com/api/v1/products?page=1&size=10`
- REST categories endpoint: `https://backend.ecom.subraatakumar.com/api/v1/categories`
- GraphQL endpoint: `https://backend.ecom.subraatakumar.com/graphql`
- GraphiQL playground: `https://backend.ecom.subraatakumar.com/graphiql`

## Lessons

1. [REST vs GraphQL](./lessons/lesson1_1.md)
2. [Problems with REST in Mobile Apps](./lessons/lesson1_2.md)
3. [How GraphQL Solves Mobile API Problems](./lessons/lesson1_3.md)
4. [Understanding GraphQL Architecture](./lessons/lesson1_4.md)
5. [Practical: Build the Same Screen with REST and GraphQL](./lessons/lesson1_5.md)

## External references

- [GraphQL official learning guide](https://graphql.org/learn/)
- [Apollo Client documentation](https://www.apollographql.com/docs/react)
- [Expo create project documentation](https://docs.expo.dev/get-started/create-a-project/)

## Module outcome

By the end of this module, you should be able to explain why a product listing screen can be easier to build with GraphQL than with REST, especially when the screen needs only a small part of a larger API response.
