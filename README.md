<div align="center">

# GraphQL for React Native Developers

**A free micro course by [RN Mastery](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers)**

Learn GraphQL the way React Native developers actually use it: real screens, real Apollo Client code, and a real e-commerce GraphQL API already waiting for you.

[![Free Forever](https://img.shields.io/badge/Free-Forever-brightgreen?style=flat-square)](#)
[![Modules](https://img.shields.io/badge/Modules-15-blue?style=flat-square)](#course-curriculum)
[![API](https://img.shields.io/badge/API-Ready-made%20GraphQL-orange?style=flat-square)](#ready-made-api)
[![Level](https://img.shields.io/badge/Level-Beginner%20to%20Intermediate-purple?style=flat-square)](#prerequisites)

</div>

---

## What this course is

Most GraphQL tutorials start with backend architecture and schema design.
This one starts where React Native developers actually live: screen rendering, Apollo Client, cache updates, pagination, auth, offline behavior, and API integration.

You will use a ready-made GraphQL API hosted at:

- GraphiQL: `https://backend.ecom.subraatakumar.com/graphiql`
- GraphQL API: `https://backend.ecom.subraatakumar.com/graphql`

The backend is an e-commerce domain, so every lesson can map to real mobile app features:

- Browse products
- Filter by category, price, or search term
- Sign in and refresh tokens
- Fetch orders and order status
- Create, cancel, return, and manage orders
- Work with chat configuration and message history

This course is not about becoming a GraphQL backend engineer first.
It is about building production-ready mobile apps that consume GraphQL cleanly.

---

## Why this version is different

For React Native developers, GraphQL is most valuable when it reduces client complexity.

This course emphasizes:

- Fewer API calls
- Better screen-specific data fetching
- Strong cache behavior in mobile apps
- Production auth patterns
- Pagination that fits `FlatList`
- Typed operations with GraphQL Code Generator
- Working with a live schema instead of inventing a toy backend

The balance is intentional:

- 30% GraphQL theory
- 70% React Native implementation

---

## Ready-made API

The backend already provides the operations you need for the course. The most important ones are:

GraphiQL also includes starter examples for the main queries and mutations, including auth, products, orders, and chat setup.

### Queries

- `health`
- `categories`
- `allProducts`
- `products(page, size, limit, category, minPrice, maxPrice, q)`
- `orders`
- `orderStatus(id)`
- `chatMessages(chatKey)`

### Mutations

- `signUp(input)`
- `signIn(input)`
- `refreshToken(token)`
- `logout(token)`
- `createOrder(input)`
- `cancelOrder(id)`
- `returnOrder(id, input)`
- `outForDelivery(id, input)`
- `deliverOrder(id)`
- `rejectOrder(id, input)`
- `configureChat(mongodbUri)`

### Subscription schema

- `newMessage(chatKey)`

The schema includes the subscription field for learning and architecture discussions. The hosted course flow should treat real-time transport as a topic to understand, not something you need to build from scratch for every lesson.

### Auth header

Protected requests use:

```http
Authorization: Bearer <accessToken>
```

### Core data model

- `Product`: `id`, `name`, `price`, `category`, `imageUrl`, `description`, `brand`, `inStock`, `quantity`, `unit`, `discount`, `rating`
- `Order`: `id`, `userId`, `products`, `paymentMode`, `status`, `assignedStaff`, `createdAt`, `deliveredAt`, `statusHistory`
- `Message`: `content`, `username`, `createdAt`

---

## Who this is for

You are a React Native developer who has either:

- never used GraphQL in a real app
- copied Apollo Client setup without understanding the cache
- used REST for everything and wants to compare the ergonomics
- joined a team with an existing GraphQL backend and needs to become productive fast

This course is also useful if you already know Apollo Client and want to get better at the parts that matter in mobile apps: pagination, cache updates, auth, and error handling.

---

## Prerequisites

| Skill | Level needed |
|---|---|
| React Native | Comfortable - you have built screens before |
| React Hooks (`useState`, `useEffect`) | Comfortable |
| JavaScript async/await | Comfortable |
| TypeScript | Helpful, but not required |
| Backend / Node.js | Not required |

---

## What you'll build

### Module 1 practical
A React Native screen implemented once with REST and once with GraphQL so students can see the difference immediately.

### Module 4 practical
A product catalog experience with a product list screen and a product details screen backed by the real GraphQL API.

### Module 5 practical
A production-style order flow:

- Create order
- Cancel order while it is still preparing
- Return delivered orders
- Inspect status history

### Final capstone
A complete e-commerce React Native app with:

- Authentication
- Product browsing
- Search and filters
- Pagination and infinite scrolling
- Order history and order status
- Role-aware order actions
- Chat support surface
- Apollo cache updates
- GraphQL Codegen
- TypeScript
- Offline-friendly data handling

---

## Course Curriculum

### Module 1 - Why GraphQL for Mobile Apps?

The right opening for React Native developers: why GraphQL often fits mobile screens better than REST.

[Open Module 1 lectures](./modules/module1/readme.md)

**Topics**

- REST vs GraphQL
- Over-fetching
- Under-fetching
- Multiple API calls
- How GraphQL solves mobile data-shaping problems
- Understanding GraphQL architecture

**Practical**

Build the same React Native screen twice: once with REST and once with GraphQL.

---

### Module 2 - GraphQL Fundamentals

Learn GraphQL through the live e-commerce schema instead of abstract examples.

**Topics**

- Schema
- Types
- Scalars
- Queries
- Mutations
- Arguments
- Variables
- Aliases
- Fragments

**Practical**

Query product catalog and auth response data from the GraphQL API using the actual schema.

---

### Module 3 - Setting Up Apollo Client

Set up Apollo in a React Native app with the right mental model from day one.

**Topics**

- What is Apollo Client?
- Installation
- Apollo Provider
- Apollo Client configuration
- HTTP links
- Error links

**Practical**

Configure Apollo Client in a React Native project and connect it to the live backend.

---

### Module 4 - Fetching Data

Build real screens that use `useQuery` against the product catalog.

**Topics**

- `useQuery`
- Loading states
- Error handling
- Refetching
- Polling
- Pagination basics

**Practical**

Build:

- Product list screen
- Product details screen

---

### Module 5 - GraphQL Mutations

Use mutations for real mobile app workflows, not toy examples.

**Topics**

- `useMutation`
- Creating records
- Updating records
- Deleting records
- Mutation responses

**Practical**

Build an order management flow:

- Create Order
- Cancel Order
- Return Order

---

### Module 6 - Apollo Cache Deep Dive

This is where most React Native developers get stuck, so we make it concrete.

**Topics**

- Normalized cache
- Cache-first strategy
- Network-first strategy
- Cache-and-network
- Updating cache after mutation
- `refetchQueries`
- `cache.modify`

**Practical**

Instant UI updates for product and order screens without unnecessary refetches.

---

### Module 7 - Pagination

Use the real `products(page, size, ...)` API to learn pagination patterns that fit mobile apps.

**Topics**

- Offset pagination
- Cursor pagination
- Infinite scrolling

**Practical**

Build an Instagram-style product feed with `FlatList` and infinite loading.

---

### Module 8 - Authentication

Learn auth the way mobile apps actually do it: tokens, refresh flows, and protected screens.

**Topics**

- JWT authentication
- Login mutation
- Refresh tokens
- Secure storage
- Token injection

**Practical**

Build a production-ready auth flow using `signIn`, `refreshToken`, and `logout`.

---

### Module 9 - Real-Time GraphQL

Understand how real-time GraphQL maps to mobile chat and support flows.

**Topics**

- Subscriptions
- WebSockets
- Apollo subscription links

**Practical**

Build a chat support surface around the backend's chat configuration and message history, and learn where subscription transport fits in a production architecture.

---

### Module 10 - GraphQL Code Generation

One of the highest-leverage topics for React Native teams.

**Topics**

- GraphQL Code Generator
- Generating TypeScript types
- Typed queries
- Typed mutations

**Practical**

Remove manual interfaces and let the schema drive your types.

---

### Module 11 - React Native Performance

Use GraphQL without making your app slower or more complex.

**Topics**

- Query optimization
- Lazy loading
- Avoiding unnecessary rerenders
- Offline strategies
- Cache persistence

**Practical**

Build an offline-friendly mobile experience with cached product and order data.

---

### Module 12 - Error Handling

Production apps need good failure states, not just a red error message.

**Topics**

- Network errors
- GraphQL errors
- Retry strategies
- User-friendly error screens

**Practical**

Add a production-grade error handling layer to the app.

---

### Module 13 - GraphQL + React Query

Compare the tools honestly and use the right one for the job.

**Topics**

- Apollo vs React Query
- When to use each
- Hybrid architectures

**Practical**

Build the same app surface with both approaches and compare tradeoffs.

---

### Module 14 - Testing GraphQL Apps

Testing should be part of the flow, not an afterthought.

**Topics**

- `MockedProvider`
- Unit testing
- Integration testing
- MSW GraphQL

**Practical**

Test a complete product or order feature end to end.

---

### Module 15 - Backend Awareness

Not deep backend development, just enough to work well with backend teams and read the schema confidently.

**Topics**

- GraphQL server basics
- Resolvers
- Schema design
- N+1 problem
- DataLoader

**Practical**

Inspect the provided backend schema and understand how the resolvers power the mobile app.

---

## Final Capstone Project

Build a complete React Native e-commerce application on top of the ready-made GraphQL API.

### Features

- Authentication
- Product catalog
- Search and category filtering
- Product details
- Infinite feed
- Order history
- Order status tracking
- Role-based order actions
- Chat support
- Offline support
- GraphQL Codegen
- TypeScript
- Apollo cache

### What students learn

- GraphQL queries
- GraphQL mutations
- Authentication
- Cache management
- Pagination
- Error handling
- Offline-first patterns
- Production app structure

---

## Tech Stack

| Tool | Purpose |
|---|---|
| [Apollo Client](https://www.apollographql.com/docs/react/) | GraphQL client for queries, mutations, and cache |
| [graphql](https://github.com/graphql/graphql-js) | Core GraphQL library |
| [graphql-ws](https://github.com/enisdenjo/graphql-ws) | WebSocket transport for subscription-based architectures |
| [GraphQL Code Generator](https://the-guild.dev/graphql/codegen) | Generate TypeScript types from the schema |
| React Native / Expo | Mobile app framework |

---

## How to use this course

This course is designed to be read linearly, because each module builds on the previous one.

- Already know REST vs GraphQL basics? Start at **Module 2**
- Already using Apollo Client? Jump to **Module 6** for cache and **Module 7** for pagination
- Primarily interested in auth and order flows? Focus on **Modules 8 and 5**
- Working with backend teams? Read **Module 15** after the core client modules

Every lesson should include:

- A clear goal
- A real React Native implementation
- A GraphQL operation you can run in GraphiQL
- Practical mobile tradeoffs, not abstract theory

---

## Everything is free

All modules are free. No account required to read. No paywalls.

RN Mastery is a community-first platform. The goal is to make high-quality React Native education accessible to every developer, regardless of where they are or what they can afford.

---

## About RN Mastery

[RN Mastery](https://rnm.subraatakumar.com) is a free, contributor-driven React Native learning platform built by a React Native developer, for React Native developers.

If this helped you, the best thing you can do is share it with another developer who is trying to level up.

---

## Found an issue?

If you spot a bug, a broken link, or an explanation that does not land, open an issue or reach out directly. Every piece of feedback makes the course better for the next person.

---

<div align="center">

**[Start Learning -> GraphiQL](https://backend.ecom.subraatakumar.com/graphiql)**

*Built with love by [Subrata Kumar](https://subraatakumar.com)*

</div>
