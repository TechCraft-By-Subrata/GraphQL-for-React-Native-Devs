<div align="center">

# GraphQL for React Native Developers

**A free micro course by [RN Mastery](https://rnm.subraatakumar.com)**

Learn GraphQL the way React Native developers actually use it — real screens, real Apollo Client code, no backend required.

[![Free Forever](https://img.shields.io/badge/Free-Forever-brightgreen?style=flat-square)](#)
[![Lessons](https://img.shields.io/badge/Lessons-18-blue?style=flat-square)](#course-curriculum)
[![Duration](https://img.shields.io/badge/Duration-~4%20hrs-orange?style=flat-square)](#course-curriculum)
[![Level](https://img.shields.io/badge/Level-Beginner%20→%20Intermediate-purple?style=flat-square)](#prerequisites)

</div>

---

## What this course is

Most GraphQL tutorials are written for web developers and backend engineers. This one isn't.

Every concept here is taught through a React Native lens — `FlatList` pagination, `useQuery` hooks, Apollo Client setup in an Expo project, subscriptions over WebSocket on mobile, and real-world patterns you'll actually use in production.

By the end, you'll be able to take a GraphQL API, connect it to a React Native app, and build screens that are faster, cleaner, and easier to maintain than anything you'd write with REST.

---

## Who this is for

You're a React Native developer who has heard of GraphQL but never actually used it in a project. Or you've set up Apollo Client once, copy-pasted some code, and it worked — but you don't fully understand why.

This course is for you if:
- You've built React Native screens with REST APIs and want to see how GraphQL compares
- You want to stop writing `useEffect` + `fetch` chains and multiple loading states by hand
- You're preparing for a role at a company that uses GraphQL (many do)
- You want to understand Apollo Client deeply — caching, pagination, subscriptions, auth — not just the surface

This is **not** a backend course. You won't write a single line of server-side code. Everything is from the React Native client perspective.

---

## Prerequisites

| Skill | Level needed |
|---|---|
| React Native | Comfortable — you've built screens before |
| React Hooks (`useState`, `useEffect`) | Comfortable |
| JavaScript async/await | Comfortable |
| TypeScript | Helpful but not required |
| Backend / Node.js | Not required at all |

---

## What you'll build

**Module 2 mini project** — A job listings screen fetching real data from a live GraphQL API, with filtering by status and a detail view.

**Module 5 capstone** — A fully working live chat screen with real-time messages delivered via GraphQL subscriptions — the kind of feature that stands out on a resume or portfolio.

---

## Course curriculum

> All 18 lessons are **free**. No account required to read. No paywalls, ever.

---

### Module 1 — Why GraphQL? REST vs GraphQL
*~1h 45min · 5 lessons*

The foundation. Understand what problems GraphQL was built to solve — specifically on mobile — before writing a single line of Apollo code.

| # | Lesson | Duration |
|---|---|---|
| 1.1 | [The Problem with REST in Mobile Apps](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 20 min |
| 1.2 | [What GraphQL Solves: Over-fetching & Under-fetching](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 20 min |
| 1.3 | [The GraphQL Schema & Type System](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 25 min |
| 1.4 | [Queries, Mutations & Subscriptions](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 30 min |
| 1.5 | [Quiz: Module 1 Checkpoint](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 10 min |

**What you'll learn:**
- Why REST creates over-fetching, N+1 requests, and versioning pain on mobile
- How GraphQL gives the client full control over the data shape
- Reading any GraphQL schema confidently — types, scalars, `!`, lists, enums, input types
- When to reach for a `query`, `mutation`, or `subscription` — and when subscriptions are overkill

---

### Module 2 — Queries & Mutations in React Native
*~50 min · 4 lessons*

Stop reading, start building. Set up Apollo Client, write your first `useQuery` against a live API, and have a working screen by the end of Lesson 2.1.

| # | Lesson | Duration |
|---|---|---|
| 2.1 | [Setting up Apollo Client in React Native](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 15 min |
| 2.2 | [Fetching data with useQuery](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 15 min |
| 2.3 | [Writing data with useMutation + optimistic UI](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 15 min |
| 2.4 | [Mini project: Job listings screen with filter & detail](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 25 min |

**What you'll learn:**
- Installing and configuring Apollo Client step by step
- Writing named queries with variables — the right way, never string interpolation
- `useMutation` with loading states, error handling, and `onCompleted`
- Optimistic UI — making your app feel instant before the server responds

---

### Module 3 — Variables, Fragments & Type Safety
*~40 min · 3 lessons*

Level up your GraphQL code with reusable fragments and full TypeScript type safety — eliminating an entire class of runtime bugs.

| # | Lesson | Duration |
|---|---|---|
| 3.1 | [Dynamic queries with variables](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 10 min |
| 3.2 | [Reusable fragments — DRY GraphQL](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 15 min |
| 3.3 | [TypeScript + GraphQL codegen in React Native](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 20 min |

**What you'll learn:**
- Fragments — define a field set once, reuse it across multiple queries
- `graphql-codegen` — auto-generate TypeScript types from your schema so your IDE knows the shape of every query response
- Catching data shape errors at compile time, not at runtime on a user's device

---

### Module 4 — Caching, Pagination & Performance
*~55 min · 4 lessons*

Apollo Client's cache is its superpower — and its biggest source of confusion. This module demystifies it completely, then covers pagination patterns built for `FlatList`.

| # | Lesson | Duration |
|---|---|---|
| 4.1 | [Apollo InMemoryCache — how it actually works](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 15 min |
| 4.2 | [Cursor-based vs offset pagination with FlatList](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 20 min |
| 4.3 | [Polling, refetchQueries & network strategies](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 15 min |
| 4.4 | [Quiz: Apollo caching patterns](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 10 min |

**What you'll learn:**
- How Apollo normalizes and stores data — and why `id` fields matter so much
- Writing `fetchMore` for infinite scroll `FlatList` pagination
- When to use polling vs refetch on focus vs subscriptions
- `refetchQueries` — keeping related screens in sync after a mutation

---

### Module 5 — Real-world: Subscriptions & Auth
*~60 min · 4 lessons*

Real-time data and authentication — the two things that come up in every production GraphQL app.

| # | Lesson | Duration |
|---|---|---|
| 5.1 | [GraphQL subscriptions with WebSocket in React Native](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 15 min |
| 5.2 | [Authenticated GraphQL — JWT + Apollo Link](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 15 min |
| 5.3 | [Error handling & retry strategies](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 15 min |
| 5.4 | [Capstone: Live chat screen with subscriptions](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers) | 30 min |

**What you'll learn:**
- Setting up `graphql-ws` and `WebSocketLink` in React Native
- Splitting HTTP and WebSocket traffic with Apollo's `split()` link
- Attaching JWT tokens to every request via `authLink`
- Handling network errors, auth errors, and retry logic gracefully
- Building a complete live chat screen as your capstone project

---

## Tech stack

| Tool | Purpose |
|---|---|
| [Apollo Client](https://www.apollographql.com/docs/react/) | GraphQL client — queries, mutations, caching |
| [graphql](https://github.com/graphql/graphql-js) | Core GraphQL library |
| [graphql-ws](https://github.com/enisdenjo/graphql-ws) | WebSocket transport for subscriptions |
| [graphql-codegen](https://the-guild.dev/graphql/codegen) | TypeScript type generation from schema |
| React Native / Expo | Mobile app framework |

---

## How to use this course

This course is designed to be read linearly — each lesson builds on the previous one. That said:

- Already know REST vs GraphQL basics? Start at **Module 2**
- Already using Apollo Client but want to go deeper? Jump to **Module 3 or 4**
- Just here for subscriptions and auth? **Module 5** is self-contained enough to read standalone

Every lesson has:
- A clear "what you'll learn" section up front
- Real-world React Native code you can copy into a project
- A React Native–specific angle, not generic GraphQL theory
- Resource links to go deeper

---

## Everything is free

All 18 lessons, both projects, all quizzes — completely free. No account required to read. RN Mastery is a community-first platform. The goal is to make high-quality React Native education accessible to every developer, regardless of where they are or what they can afford.

---

## About RN Mastery

[RN Mastery](https://rnm.subraatakumar.com) is a free, contributor-driven React Native learning platform — built by a React Native developer, for React Native developers.

If this helped you, the best thing you can do is share it with another developer who's trying to level up.

---

## Found an issue?

Spotted a bug in the code, a broken link, or an explanation that didn't land? Open an issue or reach out directly — every piece of feedback makes the course better for the next person.

---

<div align="center">

**[Start Learning → Lesson 1.1](https://rnm.subraatakumar.com/courses/graphql-for-react-native-developers)**

*Built with ❤️ by [Subrata Kumar](https://subraatakumar.com)*

</div>
