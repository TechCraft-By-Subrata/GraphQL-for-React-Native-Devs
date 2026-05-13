
## Lesson 1.4 — Queries, Mutations & Subscriptions

### What you'll learn
- The three operation types in GraphQL and the exact difference between them
- How to write a proper named query with variables — the right way, not shortcuts
- How mutations return updated data (and why that's useful in React Native)
- When subscriptions make sense vs when they're overkill
- How all three map to real screens and interactions in a mobile app

---

### The three operations — a clear mental model

Every interaction between your React Native app and a GraphQL API is one of three things:

| Operation | Purpose | HTTP analogy | When to use |
|---|---|---|---|
| `query` | Read data | GET | Loading any screen |
| `mutation` | Write / change data | POST / PUT / DELETE | Button taps, form submits |
| `subscription` | Real-time data stream | WebSocket | Live chat, notifications |

Let's go deep on each one.

---

### Queries — reading data

A query is how your React Native app loads data. Every screen that displays information starts with a query.

#### The wrong way (shorthand)

```graphql
# This works but is a bad habit — hard to debug, can't add variables
{
  jobs {
    title
    company {
      name
    }
  }
}
```

#### The right way (named query with operation type)

```graphql
query GetJobListings {
  jobs {
    id
    title
    remote
    salary
    company {
      name
      logo
    }
  }
}
```

Always name your queries. It makes debugging easier — in Apollo DevTools and server logs, you'll see `GetJobListings` instead of anonymous query.

#### Queries with variables

Most real queries need dynamic input — a user ID, a search term, a filter. Variables are how you pass them cleanly:

```graphql
# Define the variable at the top with its type
query GetJobDetail($jobId: ID!) {
  job(id: $jobId) {
    id
    title
    description
    salary
    remote
    status
    postedAt
    company {
      name
      logo
      website
    }
  }
}
```

In your React Native component, you pass variables separately from the query — never by string concatenation:

```javascript
// JobDetailScreen.js
import { useQuery, gql } from '@apollo/client';

const GET_JOB_DETAIL = gql`
  query GetJobDetail($jobId: ID!) {
    job(id: $jobId) {
      id
      title
      description
      salary
      remote
      status
      company {
        name
        logo
        website
      }
    }
  }
`;

export default function JobDetailScreen({ route }) {
  const { jobId } = route.params;

  const { data, loading, error } = useQuery(GET_JOB_DETAIL, {
    variables: { jobId },   // passed separately — safe, typed, debuggable
  });

  if (loading) return <ActivityIndicator />;
  if (error) return <Text>Something went wrong</Text>;

  const job = data.job;

  return (
    <ScrollView>
      <Image source={{ uri: job.company.logo }} />
      <Text>{job.title}</Text>
      <Text>{job.company.name}</Text>
      <Text>{job.remote ? '🌍 Remote' : '🏢 On-site'}</Text>
      <Text>{job.salary ? `₹${job.salary.toLocaleString()} / yr` : 'Salary not disclosed'}</Text>
      <Text>{job.description}</Text>
    </ScrollView>
  );
}
```

Notice how the component has zero data-fetching logic — no `useEffect`, no `fetch`, no `useState` for loading. `useQuery` handles all of it. Your component focuses entirely on rendering.

#### Querying with filters (optional arguments)

Many queries accept optional arguments to filter or sort:

```graphql
# Filter jobs by status and remote preference
query GetFilteredJobs($status: JobStatus, $remote: Boolean) {
  jobs(status: $status, remote: $remote) {
    id
    title
    salary
    company {
      name
    }
  }
}
```

```javascript
// In your filter screen
const { data } = useQuery(GET_FILTERED_JOBS, {
  variables: {
    status: 'OPEN',
    remote: true,     // only show remote jobs
  },
});
```

---

### Mutations — writing data

A mutation is how your React Native app changes data — submitting a form, liking a post, deleting a record, logging in.

#### The key difference from a REST POST

With REST, after a POST you often need to re-fetch data. With GraphQL mutations, you get the updated data back in the same response.

```graphql
mutation ApplyForJob($input: JobApplicationInput!) {
  applyForJob(input: $input) {
    id                    # get back the new application's ID
    status                # and its initial status
    job {
      title               # and confirm which job it's for
      company {
        name
      }
    }
    appliedAt
  }
}
```

```graphql
input JobApplicationInput {
  jobId: ID!
  coverLetter: String!
  resumeUrl: String!
}
```

In your React Native "Apply" screen:

```javascript
import { useMutation, gql } from '@apollo/client';

const APPLY_FOR_JOB = gql`
  mutation ApplyForJob($input: JobApplicationInput!) {
    applyForJob(input: $input) {
      id
      status
      job {
        title
        company {
          name
        }
      }
      appliedAt
    }
  }
`;

export default function ApplyScreen({ route }) {
  const { jobId } = route.params;
  const [coverLetter, setCoverLetter] = useState('');
  const navigation = useNavigation();

  const [applyForJob, { loading, error }] = useMutation(APPLY_FOR_JOB, {
    onCompleted: (data) => {
      // Mutation succeeded — navigate to confirmation
      navigation.navigate('ApplicationConfirmed', {
        application: data.applyForJob,
      });
    },
  });

  const handleSubmit = () => {
    applyForJob({
      variables: {
        input: {
          jobId,
          coverLetter,
          resumeUrl: 'https://myresume.com/subrata.pdf',
        },
      },
    });
  };

  return (
    <View>
      <TextInput
        value={coverLetter}
        onChangeText={setCoverLetter}
        placeholder="Write your cover letter..."
        multiline
      />
      {error && <Text style={{ color: 'red' }}>Failed to apply. Try again.</Text>}
      <Button
        title={loading ? 'Submitting...' : 'Submit Application'}
        onPress={handleSubmit}
        disabled={loading}
      />
    </View>
  );
}
```

`useMutation` gives you a function to call, and the loading/error states. Clean, no boilerplate.

#### Simple mutations — like/unlike, follow/unfollow

Not all mutations need input types. Simple ones just take a single argument:

```graphql
mutation LikeJob($jobId: ID!) {
  likeJob(jobId: $jobId) {
    id
    likesCount      # get back the updated count
    isLikedByMe     # and the new liked state
  }
}
```

```javascript
const [likeJob] = useMutation(LIKE_JOB);

// In your FlatList item's like button
<TouchableOpacity onPress={() => likeJob({ variables: { jobId: job.id } })}>
  <Text>❤️ {job.likesCount}</Text>
</TouchableOpacity>
```

---

### Subscriptions — real-time data

Subscriptions are how GraphQL handles real-time updates. Instead of polling (asking "anything new?" every few seconds), subscriptions push data to your app the moment something changes.

Under the hood, subscriptions use **WebSockets** — a persistent, two-way connection between your app and the server.

#### When to use subscriptions

Use subscriptions when freshness matters and delay is noticeable:

- Live chat messages
- Real-time notifications ("someone liked your post")
- Live bidding / auction updates
- Collaborative editing (like Notion or Figma)
- Delivery tracking ("your order is 5 mins away")

#### When NOT to use subscriptions

Don't reach for subscriptions by default. They add complexity (WebSocket setup, reconnection logic, authentication) and drain battery on mobile. Ask yourself: "would the user notice a 30-second delay?" If no — use a query with a refetch on focus instead.

- A job listings page refreshing every time the user opens the app → **query + refetch on focus**
- A notifications badge count → **query with polling (every 60s)**
- A live chat screen → **subscription** ✅

#### What a subscription looks like

```graphql
subscription OnNewJobPosted {
  newJob {
    id
    title
    remote
    company {
      name
      logo
    }
  }
}
```

```javascript
import { useSubscription, gql } from '@apollo/client';

const ON_NEW_JOB = gql`
  subscription OnNewJobPosted {
    newJob {
      id
      title
      remote
      company {
        name
        logo
      }
    }
  }
`;

export default function LiveJobFeed() {
  const { data, loading } = useSubscription(ON_NEW_JOB);

  // Every time a new job is posted on the server,
  // this component re-renders with the new job in data.newJob
  if (loading) return <Text>Connecting...</Text>;

  return (
    <View>
      <Text>🆕 New job just posted!</Text>
      <Text>{data.newJob.title}</Text>
      <Text>{data.newJob.company.name}</Text>
    </View>
  );
}
```

We'll do a full subscription implementation with WebSocket setup in Module 5.

---

### Mapping operations to real app screens

Here's how every common React Native screen maps to a GraphQL operation:

| Screen / Action | Operation | Example |
|---|---|---|
| Home feed | `query` | `query GetFeed` |
| Profile page | `query` | `query GetUserProfile($id: ID!)` |
| Search results | `query` | `query SearchJobs($term: String!)` |
| Pull-to-refresh | `query` refetch | `.refetch()` |
| Submit a form | `mutation` | `mutation CreateJob($input: ...)` |
| Like / unlike | `mutation` | `mutation LikeJob($id: ID!)` |
| Delete a record | `mutation` | `mutation DeleteJob($id: ID!)` |
| Login | `mutation` | `mutation Login($email: String!, $password: String!)` |
| Live chat | `subscription` | `subscription OnNewMessage($roomId: ID!)` |
| Real-time notifications | `subscription` | `subscription OnNotification` |

When you're building a new screen, the first question to ask is: "Is this reading data, writing data, or receiving a real-time stream?" The answer immediately tells you which operation to reach for.

---

### Summary

- `query` — reads data. Use it for every screen load. Always name it.
- `mutation` — writes data. Returns the updated data so you don't need a re-fetch.
- `subscription` — real-time push over WebSocket. Use selectively — only when delay truly matters.
- Variables are always passed separately from the query string — never interpolated.

---

### Resources
- 📖 [Queries & Mutations — Official GraphQL Docs](https://graphql.org/learn/queries/)
- 📖 [Subscriptions — Apollo Docs](https://www.apollographql.com/docs/react/data/subscriptions/)
- 🛝 [Apollo Sandbox — run queries live against a real API](https://studio.apollographql.com/sandbox/explorer)
- 📖 [When to use subscriptions vs polling — Apollo Blog](https://www.apollographql.com/blog/graphql-subscriptions-vs-polling)

---

## Lesson 1.5 — Quiz: Module 1 Checkpoint

Test yourself before moving on. Read each question, think through your answer, then expand to check.

---

**Question 1**

Look at this schema field:

```graphql
type Company {
  jobs: [Job!]!
}
```

What do the two `!` marks mean individually? What would happen if you wrote `[Job]` instead?

<details>
<summary>Answer</summary>

The **outer `!`** means the `jobs` field itself will never be null — you'll always get an array back (possibly empty `[]`, but never `null`).

The **inner `!`** (inside `[Job!]`) means every item inside the array is a non-null Job — you'll never get a `null` value inside the list.

If you wrote `[Job]` instead, both the list and its items could be null. In React Native, this means you'd need to write defensive code like `(company.jobs ?? []).filter(Boolean).map(...)` — much messier. `[Job!]!` is the safest and most common pattern.

</details>

---

**Question 2**

Your team is building a food delivery app. Categorize each of these screens/actions as a `query`, `mutation`, or `subscription`:

- Restaurant listing page
- Placing an order
- Tracking a delivery in real-time on a map
- Searching restaurants by cuisine
- Cancelling an order
- Receiving a "your order is ready" notification

<details>
<summary>Answer</summary>

| Screen / Action | Operation | Reason |
|---|---|---|
| Restaurant listing page | `query` | Reading data |
| Placing an order | `mutation` | Writing data |
| Tracking a delivery in real-time | `subscription` | Needs live updates — delay is noticeable |
| Searching restaurants by cuisine | `query` | Reading filtered data |
| Cancelling an order | `mutation` | Changing data |
| "Your order is ready" notification | `subscription` | Real-time push event |

</details>

---

**Question 3**

What is wrong with this mutation in a React Native component?

```javascript
const orderId = route.params.id;

const CANCEL_ORDER = gql`
  mutation CancelOrder {
    cancelOrder(id: "${orderId}") {
      id
      status
    }
  }
`;
```

<details>
<summary>Answer</summary>

The `orderId` is being **string-interpolated directly into the query** — this is wrong for two reasons:

1. **Security risk:** string interpolation in queries is equivalent to SQL injection. A malicious value in `orderId` could manipulate the query.
2. **Apollo can't optimize it:** Apollo caches and deduplicates queries by their string. A dynamically interpolated query string is a different string every time — breaking caching entirely.

The correct way uses a **variable**:

```javascript
const CANCEL_ORDER = gql`
  mutation CancelOrder($id: ID!) {
    cancelOrder(id: $id) {
      id
      status
    }
  }
`;

const [cancelOrder] = useMutation(CANCEL_ORDER);

// Then call it with:
cancelOrder({ variables: { id: orderId } });
```

Variables are always passed separately — never interpolated.

</details>

---

**Question 4**

You're building a "Create Profile" screen. The user fills in their name, bio, and profile picture URL. Write the `input` type and `mutation` definition in SDL for this.

<details>
<summary>Answer</summary>

```graphql
input CreateProfileInput {
  name: String!
  bio: String          # optional — user might skip it
  avatarUrl: String    # optional — might not have a photo yet
}

type Mutation {
  createProfile(input: CreateProfileInput!): User!
}
```

Key decisions:
- `name` is required (`String!`) — a profile without a name doesn't make sense
- `bio` and `avatarUrl` are optional (no `!`) — reasonable to skip on first signup
- The mutation returns `User!` — so your component gets the full created user back without a separate fetch

</details>

---

**Question 5**

Your tech lead says: "Let's add a subscription so the job listings page updates in real-time whenever a new job is posted." Is this a good idea? What would you say?

<details>
<summary>Answer</summary>

**Probably not** — and it's worth pushing back. Here's the reasoning:

A job listings page doesn't need sub-second freshness. Users browsing jobs won't notice if the list is 30–60 seconds stale. Subscriptions add real costs:
- WebSocket connections drain mobile battery
- They require more complex setup (WebSocket link, reconnection logic, auth over WS)
- They add server load (maintaining persistent connections for every client)

A better approach:
- **Query + refetch on focus** — re-run the query every time the user navigates back to the screen using React Navigation's `useFocusEffect`
- **Polling** — if the PM really wants live updates, set `pollInterval: 60000` on `useQuery` (refetches every 60 seconds)

Subscriptions shine for chat, live notifications, and real-time collaboration — where a 5-second delay would break the user experience. For a job board, polling or focus-based refetch is the right tool.

</details>

---

### Module 1 — Complete ✅

**What you covered:**
- Why REST creates over-fetching, N+1, and versioning problems in mobile apps
- How GraphQL solves all three with a single typed endpoint
- Reading a GraphQL schema — types, scalars, `!`, lists, enums, input types
- The three operations: `query` (read), `mutation` (write), `subscription` (real-time) — and when to reach for each

**Coming up in Module 2:**
You'll stop reading about GraphQL and start building with it. We'll install and configure Apollo Client in a real React Native project, write your first `useQuery` hook against a live public API, and have a working screen fetching real data by the end of the first lesson.

---

*Built with ❤️ for [RN Mastery](https://rnm.subraatakumar.com) — Free React Native learning for everyone.*