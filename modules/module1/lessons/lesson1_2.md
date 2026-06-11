# Lesson 1.2 - Problems with REST in Mobile Apps

## Goal

Understand the common API problems that show up in React Native apps when screens depend on REST endpoints.

## Problem 1: Over-fetching

Over-fetching means the API sends more data than the screen needs.

Imagine a product card that only displays:

- Product image
- Product name
- Price

But the REST endpoint returns:

- `id`
- `name`
- `price`
- `category`
- `imageUrl`
- `description`
- `brand`
- `inStock`
- `quantity`
- `unit`
- `discount`
- `rating`

The extra fields may be useful somewhere else, but this screen does not need all of them.

On mobile, extra data matters because users may be on:

- Slow network
- Expensive mobile data
- Low-end devices
- Battery-sensitive conditions

## Problem 2: Under-fetching

Under-fetching means one endpoint does not return enough data for the screen.

Example product home screen:

- Product list
- Category chips
- Logged-in user's latest orders

With REST, this can become multiple requests:

```txt
GET /api/v1/products?page=1&size=10
GET /api/v1/categories
GET /api/v1/orders
```

Each request has its own loading, error, retry, and race condition concerns.

## Problem 3: Multiple API calls

Multiple calls are not always wrong, but they create real app complexity.

In React Native, multiple calls often produce code like this:

```tsx
const [products, setProducts] = useState([]);
const [categories, setCategories] = useState([]);
const [loadingProducts, setLoadingProducts] = useState(false);
const [loadingCategories, setLoadingCategories] = useState(false);
const [productsError, setProductsError] = useState<string | null>(null);
const [categoriesError, setCategoriesError] = useState<string | null>(null);
```

This gets harder when the screen grows.

You must decide:

- Is the whole screen loading?
- Is only one section loading?
- What happens if products load but categories fail?
- Should pull-to-refresh retry every request?
- Should one failed request block the whole screen?

## Problem 4: Screen-specific data needs

Different mobile screens need different versions of the same resource.

Product list screen:

```txt
id, name, price, imageUrl
```

Product details screen:

```txt
id, name, price, imageUrl, description, brand, rating, discount, inStock
```

Cart screen:

```txt
id, name, price, quantity, inStock
```

With REST, backend teams often solve this by creating more endpoints or adding query parameters. That can work, but over time it may produce a large API surface.

## Problem 5: App versioning

Mobile apps stay installed for a long time.

If a backend changes a REST response, old app versions may break. This is why mobile teams often need careful API versioning.

GraphQL does not remove the need for discipline, but it helps because the client explicitly lists the fields it uses. Backend teams can add new fields without changing existing queries.

## REST screen example

This example fetches products and categories separately.

```tsx
import { useEffect, useState } from "react";
import { FlatList, Text, View } from "react-native";

type Product = {
  id: number;
  name: string;
  price: number | string;
  imageUrl?: string;
};

export function RestProductHomeScreen() {
  const [products, setProducts] = useState<Product[]>([]);
  const [categories, setCategories] = useState<string[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    async function load() {
      try {
        setLoading(true);
        setError(null);

        const [productResponse, categoryResponse] = await Promise.all([
          fetch("https://backend.ecom.subraatakumar.com/api/v1/products?page=1&size=10"),
          fetch("https://backend.ecom.subraatakumar.com/api/v1/categories"),
        ]);

        if (!productResponse.ok || !categoryResponse.ok) {
          throw new Error("Failed to load home screen data");
        }

        const productJson = await productResponse.json();
        const categoryJson = await categoryResponse.json();

        setProducts(productJson.data ?? []);
        setCategories(categoryJson.data ?? categoryJson ?? []);
      } catch (err) {
        setError(err instanceof Error ? err.message : "Unknown error");
      } finally {
        setLoading(false);
      }
    }

    load();
  }, []);

  if (loading) return <Text>Loading...</Text>;
  if (error) return <Text>{error}</Text>;

  return (
    <View>
      <Text>Categories: {categories.join(", ")}</Text>
      <FlatList
        data={products}
        keyExtractor={(item) => String(item.id)}
        renderItem={({ item }) => (
          <Text>
            {item.name} - Rs {item.price}
          </Text>
        )}
      />
    </View>
  );
}
```

## Check your understanding

- What is over-fetching?
- What is under-fetching?
- Why do multiple requests complicate loading and error states?
- Why does mobile app versioning make API changes harder?
