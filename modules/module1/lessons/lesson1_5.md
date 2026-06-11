# Lesson 1.5 - Practical: Build the Same Screen with REST and GraphQL

## Goal

Build a simple React Native product list screen twice:

- First with REST
- Then with GraphQL

This makes the tradeoff visible instead of theoretical.

## What the screen will show

Each product row will show:

- Product name
- Price
- Category
- Stock status

## Step 1: Create a React Native project

If you already have an Expo app, use it.

If not, create one:

```bash
npx create-expo-app graphql-mobile-demo
cd graphql-mobile-demo
npx expo start
```

Open the app in Expo Go or a simulator.

External reference: [Expo create project documentation](https://docs.expo.dev/get-started/create-a-project/)

## Step 2: Create a basic product type

Create a file named `src/types.ts`.

```ts
export type Product = {
  id: number;
  name: string;
  price: number | string;
  category: string;
  inStock: boolean;
};
```

## Step 3: Build the REST version

Create `src/RestProductScreen.tsx`.

```tsx
import { useEffect, useState } from "react";
import { ActivityIndicator, FlatList, Pressable, Text, View } from "react-native";
import type { Product } from "./types";

type ProductsResponse = {
  data?: Product[];
};

export function RestProductScreen() {
  const [products, setProducts] = useState<Product[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  async function loadProducts() {
    try {
      setLoading(true);
      setError(null);

      const response = await fetch(
        "https://backend.ecom.subraatakumar.com/api/v1/products?page=1&size=10"
      );

      if (!response.ok) {
        throw new Error("REST request failed");
      }

      const json: ProductsResponse = await response.json();
      setProducts(json.data ?? []);
    } catch (err) {
      setError(err instanceof Error ? err.message : "Failed to load products");
    } finally {
      setLoading(false);
    }
  }

  useEffect(() => {
    loadProducts();
  }, []);

  if (loading) {
    return <ActivityIndicator style={{ marginTop: 48 }} />;
  }

  if (error) {
    return (
      <View style={{ padding: 24, gap: 12 }}>
        <Text>{error}</Text>
        <Pressable onPress={loadProducts}>
          <Text>Try again</Text>
        </Pressable>
      </View>
    );
  }

  return (
    <FlatList
      contentContainerStyle={{ padding: 16, gap: 12 }}
      data={products}
      keyExtractor={(item) => String(item.id)}
      renderItem={({ item }) => (
        <View style={{ padding: 16, borderWidth: 1, borderColor: "#ddd" }}>
          <Text style={{ fontWeight: "700" }}>{item.name}</Text>
          <Text>Rs {item.price}</Text>
          <Text>{item.category}</Text>
          <Text>{item.inStock ? "In stock" : "Out of stock"}</Text>
        </View>
      )}
    />
  );
}
```

## Step 4: Use the REST screen

In `App.tsx`:

```tsx
import { SafeAreaView } from "react-native";
import { RestProductScreen } from "./src/RestProductScreen";

export default function App() {
  return (
    <SafeAreaView style={{ flex: 1 }}>
      <RestProductScreen />
    </SafeAreaView>
  );
}
```

Run the app and confirm that products appear.

## Step 5: Test the GraphQL query in GraphiQL

Open:

```txt
https://backend.ecom.subraatakumar.com/graphiql
```

Run:

```graphql
query ProductListForMobile {
  products(page: 1, size: 10) {
    data {
      id
      name
      price
      category
      inStock
    }
  }
}
```

Notice that the query only asks for the fields the screen uses.

## Step 6: Build the GraphQL version with fetch

Apollo Client starts in Module 3. For now, use `fetch` so the GraphQL request is visible.

Create `src/GraphQLProductScreen.tsx`.

```tsx
import { useEffect, useState } from "react";
import { ActivityIndicator, FlatList, Pressable, Text, View } from "react-native";
import type { Product } from "./types";

type GraphQLProductsResponse = {
  data?: {
    products: {
      data: Product[];
    };
  };
  errors?: { message: string }[];
};

const PRODUCT_LIST_QUERY = `
  query ProductListForMobile {
    products(page: 1, size: 10) {
      data {
        id
        name
        price
        category
        inStock
      }
    }
  }
`;

export function GraphQLProductScreen() {
  const [products, setProducts] = useState<Product[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  async function loadProducts() {
    try {
      setLoading(true);
      setError(null);

      const response = await fetch("https://backend.ecom.subraatakumar.com/graphql", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          query: PRODUCT_LIST_QUERY,
        }),
      });

      const json: GraphQLProductsResponse = await response.json();

      if (json.errors?.length) {
        throw new Error(json.errors[0].message);
      }

      setProducts(json.data?.products.data ?? []);
    } catch (err) {
      setError(err instanceof Error ? err.message : "Failed to load products");
    } finally {
      setLoading(false);
    }
  }

  useEffect(() => {
    loadProducts();
  }, []);

  if (loading) {
    return <ActivityIndicator style={{ marginTop: 48 }} />;
  }

  if (error) {
    return (
      <View style={{ padding: 24, gap: 12 }}>
        <Text>{error}</Text>
        <Pressable onPress={loadProducts}>
          <Text>Try again</Text>
        </Pressable>
      </View>
    );
  }

  return (
    <FlatList
      contentContainerStyle={{ padding: 16, gap: 12 }}
      data={products}
      keyExtractor={(item) => String(item.id)}
      renderItem={({ item }) => (
        <View style={{ padding: 16, borderWidth: 1, borderColor: "#ddd" }}>
          <Text style={{ fontWeight: "700" }}>{item.name}</Text>
          <Text>Rs {item.price}</Text>
          <Text>{item.category}</Text>
          <Text>{item.inStock ? "In stock" : "Out of stock"}</Text>
        </View>
      )}
    />
  );
}
```

## Step 7: Use the GraphQL screen

In `App.tsx`:

```tsx
import { SafeAreaView } from "react-native";
import { GraphQLProductScreen } from "./src/GraphQLProductScreen";

export default function App() {
  return (
    <SafeAreaView style={{ flex: 1 }}>
      <GraphQLProductScreen />
    </SafeAreaView>
  );
}
```

Run the app again.

The UI should look almost the same. The difference is how the data is requested.

## Step 8: Compare the two approaches

REST version:

- Calls a fixed endpoint
- Receives the endpoint's response shape
- May receive fields the screen does not use
- Uses standard HTTP patterns

GraphQL version:

- Calls one GraphQL endpoint
- Sends a query in the request body
- Requests only the fields used by the screen
- Gets a response shape that mirrors the query

## Step 9: Change the UI and query

Add `rating` to the product card.

In GraphQL, update only the query and UI:

```graphql
query ProductListForMobile {
  products(page: 1, size: 10) {
    data {
      id
      name
      price
      category
      inStock
      rating
    }
  }
}
```

Update the type:

```ts
export type Product = {
  id: number;
  name: string;
  price: number | string;
  category: string;
  inStock: boolean;
  rating?: number;
};
```

Update the row:

```tsx
<Text>Rating: {item.rating ?? "No rating"}</Text>
```

This is the habit you want to build: screen changes should make you think clearly about the exact data the screen needs.

## Common beginner mistakes

- Forgetting `method: "POST"` for GraphQL requests
- Forgetting the `Content-Type: application/json` header
- Looking for products at `json.products` instead of `json.data.products`
- Ignoring `json.errors`
- Asking for too many fields because they might be useful later

## Assignment

Modify the GraphQL screen so it shows:

- `name`
- `price`
- `brand`
- `discount`
- `rating`

Then remove `category` and `inStock` from the query if the UI no longer uses them.

## What you should understand before Module 2

- REST and GraphQL can both power React Native apps.
- REST gives you endpoint-shaped data.
- GraphQL gives you query-shaped data.
- GraphQL works best when each screen asks for the exact fields it renders.
- We used `fetch` here for clarity. Apollo Client will make this much nicer in later modules.
