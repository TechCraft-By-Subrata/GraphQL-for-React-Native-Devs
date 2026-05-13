## Lesson 1.2 — What GraphQL Solves: Over-fetching & Under-fetching

### What you'll learn
- What GraphQL actually is (in plain language)
- How it solves the exact problems from Lesson 1.1
- Your first real GraphQL query — written step by step
- A side-by-side comparison: REST vs GraphQL for the same feed screen

---

### What is GraphQL?

GraphQL is a **query language for your API** — invented by Facebook in 2012 and open-sourced in 2015. They built it because they were running into the exact REST problems we described, but at Facebook scale with hundreds of millions of mobile users.

The key idea: **instead of the server deciding what data to send, the client specifies exactly what it needs.**

Think of it like this:

> **REST:** You go to a restaurant, and the chef brings you a fixed "combo meal" — whether you want all of it or not.
>
> **GraphQL:** You go to the same restaurant, but now you tell the chef exactly what you want: "I'll have the rice, the dal, and the raita — skip the papad."

One endpoint. You ask for what you need. You get exactly that.

---

### The GraphQL solution to the feed screen

Remember the 61-request mess? Here's the same feed screen data — in a single GraphQL query:

```graphql
query GetFeed {
  posts(limit: 20) {
    id
    text
    createdAt
    author {
      name
      avatar
    }
    likesCount
    commentsCount
    isLikedByMe
  }
}
```

**One request. One response. Exactly the fields you need.**

The server response:

```json
{
  "data": {
    "posts": [
      {
        "id": "post_1",
        "text": "Just shipped a new React Native feature 🚀",
        "createdAt": "2024-01-15T10:30:00Z",
        "author": {
          "name": "Priya Sharma",
          "avatar": "https://cdn.example.com/priya.jpg"
        },
        "likesCount": 142,
        "commentsCount": 23,
        "isLikedByMe": false
      },
      {
        "id": "post_2",
        "text": "GraphQL changed how I think about mobile APIs",
        "createdAt": "2024-01-15T09:15:00Z",
        "author": {
          "name": "Ravi Nair",
          "avatar": "https://cdn.example.com/ravi.jpg"
        },
        "likesCount": 87,
        "commentsCount": 11,
        "isLikedByMe": true
      }
    ]
  }
}
```

No over-fetching (only `name` and `avatar` from the user, not 10 other fields). No under-fetching (author details are nested right inside the post). No stitching logic in your component.

---

### How it solves each problem

#### ✅ Solves over-fetching
You declare exactly which fields you want. The server sends only those.

```graphql
# Only want name and avatar? Ask for only those.
query {
  user(id: "42") {
    name
    avatar
  }
}
```

```json
{
  "data": {
    "user": {
      "name": "Priya Sharma",
      "avatar": "https://cdn.example.com/priya.jpg"
    }
  }
}
```

No `email`, no `phone`, no `address`. Exactly what your UI needs.

#### ✅ Solves under-fetching (N+1)
GraphQL lets you traverse relationships in a single query. The server resolves them — not your client.

```graphql
query {
  posts(limit: 20) {
    text
    author {        # nested relationship — resolved server-side
      name
      avatar
    }
    comments {      # another nested relationship
      count
    }
  }
}
```

One network request. The GraphQL server makes the database calls, not your app.

#### ✅ Solves versioning
GraphQL APIs are **versionless by design**. You add new fields to your schema without removing old ones. Old app versions keep working. New versions use the new fields.

```graphql
# Old app version asks for:
query { user(id: "42") { name avatar } }

# New app version asks for the new field too:
query { user(id: "42") { name avatar bio followersCount } }

# Both work against the same API. No /v1 /v2 needed.
```

#### ✅ Solves multiple endpoints
GraphQL has a **single endpoint** (usually `/graphql`). All queries, mutations, and subscriptions go through it. Your network layer becomes dramatically simpler.

---

### The React Native component — GraphQL way

Here's that same feed screen, now with GraphQL (using Apollo Client — we'll set this up in Module 2):

```javascript
// FeedScreen.js — the GraphQL way ✨
import React from 'react';
import { FlatList, View, Text, Image, ActivityIndicator } from 'react-native';
import { useQuery, gql } from '@apollo/client';

// Define what you need — all in one place
const GET_FEED = gql`
  query GetFeed {
    posts(limit: 20) {
      id
      text
      author {
        name
        avatar
      }
      likesCount
      commentsCount
      isLikedByMe
    }
  }
`;

export default function FeedScreen() {
  const { data, loading, error } = useQuery(GET_FEED);

  if (loading) return <ActivityIndicator />;
  if (error) return <Text>Error loading feed</Text>;

  return (
    <FlatList
      data={data.posts}
      keyExtractor={item => item.id}
      renderItem={({ item }) => (
        <View>
          <Image source={{ uri: item.author.avatar }} />
          <Text>{item.author.name}</Text>
          <Text>{item.text}</Text>
          <Text>{item.likesCount} likes</Text>
        </View>
      )}
    />
  );
}
```

Look how clean that is. No `useEffect`. No multiple fetch calls. No manual stitching. The component just declares what it needs and renders it.

---

### REST vs GraphQL — side by side

| | REST | GraphQL |
|---|---|---|
| Endpoints | Many (`/posts`, `/users`, `/likes`...) | One (`/graphql`) |
| Data fetching | Fixed response shape | Client defines the shape |
| Over-fetching | Common | Eliminated |
| N+1 requests | Common | Resolved server-side |
| Versioning | `/v1`, `/v2`... | Versionless |
| Real-time | Needs WebSocket separately | Built-in (Subscriptions) |
| Type safety | Manual / OpenAPI | Built-in schema |

---

### A common misconception

> "Does GraphQL replace REST completely?"

Not necessarily. GraphQL is a **better fit** for complex, relationship-heavy data with many consumers (like a mobile app). Simple CRUD APIs with few relationships can still work fine with REST. In the real world, you'll encounter both — and now you'll know which to choose.

---

### Resources
- 📖 [Official GraphQL Introduction](https://graphql.org/learn/)
- 📖 [How Facebook Invented GraphQL — History](https://engineering.fb.com/2015/09/14/core-infra/graphql-a-data-query-language/)
- 🛝 [GraphQL Playground — try queries live](https://studio.apollographql.com/sandbox/explorer)
- 🎥 [GraphQL Explained in 100 Seconds — Fireship](https://www.youtube.com/watch?v=eIQh02xuVw4)

---
