## Lesson 1.3 — The GraphQL Schema & Type System

### What you'll learn
- What a schema is and why it matters for React Native developers
- How to read and understand GraphQL types (you'll see these in every real project)
- The five built-in scalar types and when to use each
- How relationships between types work — and why that's powerful
- Special types: enums and input types

---

### What is the schema?

Every GraphQL API has a schema. It's a single file (or set of files) written in a language called **SDL — Schema Definition Language**. Think of it as the contract between your React Native app and the backend.

It answers three questions:
- What data exists in this system?
- How is that data related?
- What can a client do with it?

As a React Native developer, you'll read schemas constantly — to know what fields you can query, what arguments a query accepts, and what a mutation expects as input. Even if you never write a backend, you need to be comfortable reading SDL.

---

### Your first schema — reading it line by line

Let's build a schema for a simple job listings app. Something practical you might actually build.

```graphql
type Company {
  id: ID!
  name: String!
  logo: String
  website: String
  jobs: [Job!]!
}

type Job {
  id: ID!
  title: String!
  description: String!
  salary: Int
  remote: Boolean!
  company: Company!
  status: JobStatus!
  postedAt: String!
}
```

Let's break down what's happening here, one concept at a time.

---

### Scalar types — the primitives

Every field eventually resolves to a primitive value. GraphQL has five built-in scalar types:

| Type | What it holds | Real example in a job app |
|---|---|---|
| `String` | Any text | `"Senior React Native Developer"` |
| `Int` | Whole number | `120000` (salary) |
| `Float` | Decimal number | `4.8` (company rating) |
| `Boolean` | true or false | `true` (is remote?) |
| `ID` | Unique identifier | `"job_a3f9c"` |

`ID` is special — it's always serialized as a String, but it signals "this is a unique identifier, not regular text." Apollo Client uses this to power its cache.

---

### The `!` (non-null) operator

`!` is one of the most important things to understand in a schema.

```graphql
type Job {
  id: ID!          # always has a value — safe to use directly
  title: String!   # always has a value
  salary: Int      # can be null — a job might not list a salary
  logo: String     # can be null — company might not have a logo yet
}
```

**Without `!`:** the field can be `null`. In your React Native component, you must handle this:

```javascript
// salary can be null — handle it
<Text>{job.salary ? `₹${job.salary.toLocaleString()}` : 'Salary not disclosed'}</Text>

// logo can be null — use a fallback
<Image source={{ uri: job.company.logo ?? 'https://cdn.example.com/default-logo.png' }} />
```

**With `!`:** you're guaranteed a value. No null check needed.

This matters a lot in React Native — undefined fields cause crashes. Reading `!` in the schema tells you how defensive your component code needs to be.

---

### Lists

Square brackets mean a list:

```graphql
type Company {
  jobs: [Job!]!
}
```

This reads as: `jobs` is a **non-null list** of **non-null Job objects**.

There are four variations — each means something different:

```graphql
jobs: [Job]     # list can be null, items can be null
jobs: [Job!]    # list can be null, but if it exists, items are non-null
jobs: [Job]!    # list is never null, but items can be null
jobs: [Job!]!   # list is never null AND items are never null ← most common
```

In practice, you'll see `[Job!]!` most often. It means: "you'll always get an array, and every item in it is a complete Job."

---

### Object types and relationships

This is where GraphQL gets powerful. Types can reference other types, creating a graph of connected data.

```graphql
type Job {
  company: Company!   # Job → Company relationship
}

type Company {
  jobs: [Job!]!       # Company → Job[] relationship (reverse)
}
```

This is why it's called **Graph**QL. Your data is a graph of interconnected nodes.

In your React Native query, you can traverse this graph naturally:

```graphql
query GetJobDetail($id: ID!) {
  job(id: $id) {
    title
    salary
    remote
    company {           # traverse the relationship
      name
      logo
      website
    }
  }
}
```

The response nests perfectly into your component:

```javascript
// No stitching. The shape of the response matches what you asked for.
const { data } = useQuery(GET_JOB_DETAIL, { variables: { id: jobId } });

<Text>{data.job.title}</Text>
<Text>{data.job.company.name}</Text>
<Image source={{ uri: data.job.company.logo }} />
```

---

### Enum types — a fixed set of values

When a field can only be one of a specific set of values, use an enum:

```graphql
enum JobStatus {
  OPEN
  CLOSED
  DRAFT
}

type Job {
  status: JobStatus!
}
```

This gives you two guarantees:
1. The API will only ever return one of those three values
2. If someone sends an invalid value, GraphQL rejects it before it ever reaches your code

In your React Native component, you can safely use it as a string:

```javascript
// Safely compare against known enum values
const statusColor = {
  OPEN: 'green',
  CLOSED: 'red',
  DRAFT: 'gray',
};

<Text style={{ color: statusColor[job.status] }}>
  {job.status}
</Text>
```

---

### Input types — what mutations expect

When you write data (submitting a form, creating a record), you need to send structured data to the server. That's what input types are for.

```graphql
input CreateJobInput {
  title: String!
  description: String!
  salary: Int           # optional — company can choose not to disclose
  remote: Boolean!
  companyId: ID!
}

type Mutation {
  createJob(input: CreateJobInput!): Job!
  updateJob(id: ID!, input: CreateJobInput!): Job!
}
```

You'll use input types whenever you build a form in React Native that writes data — a "Post a Job" screen, a profile edit screen, a checkout form. We'll write these mutations in Module 2.

---

### Reading a real-world schema

Let's put it all together. Here's the complete schema for our job listings app — read through it and you should now understand every line:

```graphql
enum JobStatus {
  OPEN
  CLOSED
  DRAFT
}

type Company {
  id: ID!
  name: String!
  logo: String
  website: String
  description: String
  jobs: [Job!]!
}

type Job {
  id: ID!
  title: String!
  description: String!
  salary: Int
  remote: Boolean!
  status: JobStatus!
  company: Company!
  postedAt: String!
}

input CreateJobInput {
  title: String!
  description: String!
  salary: Int
  remote: Boolean!
  companyId: ID!
}

type Query {
  jobs(status: JobStatus): [Job!]!
  job(id: ID!): Job
  companies: [Company!]!
  company(id: ID!): Company
}

type Mutation {
  createJob(input: CreateJobInput!): Job!
  closeJob(id: ID!): Job!
}

type Subscription {
  newJob: Job!
}
```

You now know how to read this. The `Query`, `Mutation`, and `Subscription` types at the bottom are special — they define what operations a client can actually perform. We'll cover those in depth in the next lesson.

---

### Summary

| Concept | What it means |
|---|---|
| Schema (SDL) | The contract — describes all data and operations |
| Scalar types | Primitives: `String`, `Int`, `Float`, `Boolean`, `ID` |
| `!` (non-null) | Guarantees a value — no null check needed in your component |
| `[Type!]!` | Non-null list of non-null items |
| Object types | Your data models — `User`, `Job`, `Company` |
| Relationships | Types referencing other types — the "graph" in GraphQL |
| Enum | A field locked to a fixed set of values |
| Input type | Structured data sent with mutations (forms, create/update) |

---

### Resources
- 📖 [GraphQL Schema & Types — Official Docs](https://graphql.org/learn/schema/)
- 📖 [SDL — Schema Definition Language — Apollo](https://www.apollographql.com/docs/apollo-server/schema/schema/)
- 🛝 [GraphQL Editor — visualize your schema](https://graphqleditor.com/)
- 📖 [Nullability in GraphQL — The Guild](https://the-guild.dev/blog/graphql-nullability)

---

