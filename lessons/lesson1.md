## Lesson 1.1 — The Problem with REST in Mobile Apps

### What you'll learn
- Why REST was great — and where it starts to hurt
- The specific pain points REST creates for mobile developers
- Real-world examples from apps you already use

---

### A little context first

REST (Representational State Transfer) is how most apps talk to servers today. It's been the backbone of web APIs for 20+ years and it works well — until you're building a mobile app and data needs become complex.

Let's walk through a real scenario.

---

### Real-world scenario: Building a Twitter-like feed screen

Imagine you're building a social feed screen in React Native. Each post card needs to show:

- Post text
- Author name + avatar
- Like count
- Comment count
- Whether the current user has liked it

In a typical REST API, you might have these endpoints:

```
GET /posts           → returns list of posts (postId, text, authorId, createdAt)
GET /users/:id       → returns user details (name, avatar, bio)
GET /posts/:id/likes → returns like count + whether current user liked it
GET /posts/:id/comments/count → returns comment count
```

To render **one screen**, your React Native app makes:

```
1 call → /posts          (get 20 posts)
20 calls → /users/:id    (get author for each post)
20 calls → /posts/:id/likes
20 calls → /posts/:id/comments/count
```

**That's 61 network requests for one screen load.**

On a mobile device with limited bandwidth, variable connectivity, and battery constraints — this is a serious problem.

---

### The three core REST problems in mobile

#### 1. Over-fetching
You get more data than you need.

```javascript
// You call GET /users/42 and get back:
{
  id: 42,
  name: "Priya Sharma",
  avatar: "https://cdn.example.com/priya.jpg",
  bio: "Software Engineer at...",
  email: "priya@example.com",         // you don't need this
  phone: "+91-9876543210",            // you don't need this
  address: { city: "Bengaluru", ... }, // you don't need this
  createdAt: "2021-03-14T...",        // you don't need this
  followersCount: 1240,               // you don't need this
  followingCount: 340,                // you don't need this
  isVerified: false                   // you don't need this
}
```

Your FlatList card only needed `name` and `avatar`. You downloaded 10x more data than needed — wasting bandwidth, battery, and parse time.

#### 2. Under-fetching (the N+1 problem)
One request isn't enough, so you have to make N more.

This is exactly the 61-request scenario above. The API gives you posts, but not the author details — so you fire off 20 more requests. This is called the **N+1 problem** and it's one of the most common mobile performance killers.

```javascript
// React Native component - the N+1 anti-pattern
useEffect(() => {
  fetch('/posts')
    .then(res => res.json())
    .then(posts => {
      // Now fire N more requests 😬
      posts.forEach(post => {
        fetch(`/users/${post.authorId}`)
          .then(res => res.json())
          .then(user => {
            // update state per user... chaos
          });
      });
    });
}, []);
```

This leads to cascading loading states, race conditions, and a janky UI.

#### 3. Versioning hell
As your app evolves, the API needs to change. But you have:
- Android app v1.0 (old users who haven't updated)
- Android app v2.3 (recent release)
- iOS app v2.1

So your backend maintains `/api/v1/posts`, `/api/v2/posts`, `/api/v3/posts` — and coordinating breaking changes across mobile clients is painful.

---

### What this looks like in code (the pain)

A real React Native component struggling with REST:

```javascript
// FeedScreen.js — the REST way (painful)
import React, { useState, useEffect } from 'react';
import { FlatList, View, Text, Image, ActivityIndicator } from 'react-native';

export default function FeedScreen() {
  const [posts, setPosts] = useState([]);
  const [authors, setAuthors] = useState({});
  const [likes, setLikes] = useState({});
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    async function loadFeed() {
      try {
        // Step 1: get posts
        const postsRes = await fetch('https://api.example.com/posts');
        const postsData = await postsRes.json();

        // Step 2: get all authors in parallel (still N requests)
        const authorPromises = postsData.map(post =>
          fetch(`https://api.example.com/users/${post.authorId}`)
            .then(r => r.json())
        );
        const authorsData = await Promise.all(authorPromises);

        // Step 3: get all like counts in parallel (yet another N requests)
        const likesPromises = postsData.map(post =>
          fetch(`https://api.example.com/posts/${post.id}/likes`)
            .then(r => r.json())
        );
        const likesData = await Promise.all(likesPromises);

        // Step 4: stitch it all together manually 😩
        const authorsMap = {};
        authorsData.forEach(a => authorsMap[a.id] = a);

        const likesMap = {};
        likesData.forEach((l, i) => likesMap[postsData[i].id] = l);

        setAuthors(authorsMap);
        setLikes(likesMap);
        setPosts(postsData);
      } catch (e) {
        setError('Failed to load feed');
      } finally {
        setLoading(false);
      }
    }

    loadFeed();
  }, []);

  if (loading) return <ActivityIndicator />;
  if (error) return <Text>{error}</Text>;

  return (
    <FlatList
      data={posts}
      keyExtractor={item => item.id}
      renderItem={({ item }) => (
        <View>
          <Image source={{ uri: authors[item.authorId]?.avatar }} />
          <Text>{authors[item.authorId]?.name}</Text>
          <Text>{item.text}</Text>
          <Text>{likes[item.id]?.count} likes</Text>
        </View>
      )}
    />
  );
}
```

This is fragile, hard to maintain, and performs poorly. There's a better way.

---

### Summary

| Problem | What it means for mobile |
|---|---|
| Over-fetching | Downloads data you never use — wastes bandwidth & battery |
| Under-fetching (N+1) | Multiple round trips — slow screen loads |
| Versioning | Hard to evolve API without breaking old app versions |
| Multiple endpoints | Complex client-side stitching logic |

In the next lesson, you'll see exactly how GraphQL eliminates all four of these.

---

### Resources
- 📖 [REST API Design Best Practices — MDN](https://developer.mozilla.org/en-US/docs/Glossary/REST)
- 📖 [The N+1 Problem Explained — Shopify Engineering Blog](https://engineering.shopify.com/blogs/engineering/solving-the-n-1-problem-for-graphql-through-batching)
- 🎥 [What is REST? — Fireship (7 min)](https://www.youtube.com/watch?v=-MTSQjw5DrM)

---
