# 🛠 Redux Toolkit + RTK Query Setup in Next.js/React

This README will guide you through integrating **Redux Toolkit** and **RTK Query** into your Next.js or React project.

---

## 📂 Folder Structure

```
src/
 ├── redux/
 │   ├── api/
 │   │   └── baseApi.ts
 │   └── store.ts
 ├── lib/
 │   └── Providers.tsx
 ├── app/
 │   └── layout.tsx
 └── components/
     ├── Header.tsx
     └── Footer.tsx
```

---

## 1️⃣ Install Dependencies

Run the following command:

```bash
npm install @reduxjs/toolkit react-redux
```

---

## 2️⃣ Create the Store

**File:** `src/redux/store.ts`

```ts
import { configureStore } from '@reduxjs/toolkit';

export const store = configureStore({
  reducer: {},
});

// Infer the `RootState` and `AppDispatch` types from the store itself
export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

---

## 3️⃣ Create a Provider Component

**File:** `src/lib/Providers.tsx`

```tsx
"use client";

import { store } from "@/redux/store"; // Import from store file
import { Provider } from "react-redux";

const Providers = ({ children }: { children: React.ReactNode }) => {
  return <Provider store={store}>{children}</Provider>;
};

export default Providers;

// here store={store} I created. { children: React.ReactNode } I get from layout.tsx
// Provider from redux
```

---

## 4️⃣ Wrap the Root Layout

**File:** `src/app/layout.tsx`

```tsx
import Providers from "@/lib/Providers";
import Header from "@/components/Header";
import Footer from "@/components/Footer";
import { Roboto } from "next/font/google";

const roboto = Roboto({ subsets: ["latin"], weight: ["400", "700"] });

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <Providers>
      <html lang="en" data-theme="light">
        <body className={roboto.className}>
          <Header />
          <div className="min-h-screen">{children}</div>
          <Footer />
        </body>
      </html>
    </Providers>
  );
}
```

---

## 5️⃣ Configure `baseApi` with RTK Query

**File:** `src/redux/api/baseApi.ts`

Example using [Pokémon API](https://redux-toolkit.js.org/rtk-query/overview):

```ts
import { createApi, fetchBaseQuery } from "@reduxjs/toolkit/query/react";

export const baseApi = createApi({
  reducerPath: "baseApi",
  baseQuery: fetchBaseQuery({ baseUrl: "https://pokeapi.co/api/v2/" }),
  endpoints: (builder) => ({
    getPokemonByName: builder.query<any, string>({
      query: (name) => `pokemon/${name}`,
    }),
  }),
});

export const { useGetPokemonByNameQuery } = baseApi;
```

---

## 6️⃣ Add `baseApi` to Store Reducer & Middleware

**File:** `src/redux/store.ts`

```ts
import { configureStore } from "@reduxjs/toolkit";
import { baseApi } from "./api/baseApi";

export const store = configureStore({
  reducer: {
    [baseApi.reducerPath]: baseApi.reducer,
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(baseApi.middleware),
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

---

## ✅ Redux is Now Ready!

You can now use your API hook anywhere in your components:

```tsx
import { useGetPokemonByNameQuery } from "@/redux/api/baseApi";

export default function Pokemon() {
  const { data, error, isLoading } = useGetPokemonByNameQuery("pikachu");

  if (isLoading) return <p>Loading...</p>;
  if (error) return <p>Something went wrong!</p>;

  return <pre>{JSON.stringify(data, null, 2)}</pre>;
}
```

---

## 📝 Notes
- `Provider` **must** wrap your entire app in `layout.tsx`.
- Always add `baseApi.middleware` to the store for RTK Query.
- Type safety comes from `RootState` & `AppDispatch`.
- You can replace the Pokémon API with your own backend API.

---
