# Section 4 — Next.js App Router (AI Assisted)

**This is Pass 2.** You may use AI tools to complete or improve your answers.

- **Permitted model:** Claude Sonnet only
- **Other AI tools (ChatGPT, Gemini, Copilot, etc.) are not permitted**

Refer to [../README.md](../README.md) for the full questions.

---

## 4.1 — Data Fetching and Caching

```txt
// Include file structure here
app/
├── products/
│   ├── page.tsx      // Server Component (Data Fetching)
│   ├── loading.tsx   // Instant Loading UI
│   └── error.tsx     // Error Boundary
```

```tsx
// Include page.tsx here
import { notFound } from "next/navigation";

interface Product {
  id: string;
  title: string;
  description: string;
}

// 2. Implement ISR with a 60-second revalidation window
export const revalidate = 60; 

export default async function Page() {
  // 1. Server Component fetching from external API
  const res = await fetch("https://api.example.com/products", {
    // Alternatively, you can set revalidate here:
    // next: { revalidate: 60 }
  });

  if (!res.ok) {
    // This triggers the nearest error.tsx
    throw new Error("Failed to fetch products");
  }

  const products: Product[] = await res.json();

  if (!products.length) return notFound();

  return (
    <main className="p-8">
      <h1 className="text-2xl font-bold">Products</h1>
      <ul className="mt-4 gap-4 grid">
        {products.map((p) => (
          <li key={p.id} className="border p-4 rounded">{p.title}</li>
        ))}
      </ul>
    </main>
  );
}
```

```tsx
// Include loading.tsx here
// 3. Add a loading state using file-based conventions
export default function Loading() {
  return (
    <div className="p-8 animate-pulse">
      <div className="h-8 w-48 bg-gray-200 rounded mb-6" />
      <div className="space-y-4">
        {[1, 2, 3].map((i) => (
          <div key={i} className="h-20 bg-gray-100 rounded" />
        ))}
      </div>
    </div>
  );
}
```

```tsx
// Include error.tsx here
"use client"; // Error components must be Client Components

import { useEffect } from "react";

// 4. Handle errors gracefully
export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  useEffect(() => {
    console.error(error);
  }, [error]);

  return (
    <div className="p-8 text-center">
      <h2 className="text-red-600 font-bold">Something went wrong!</h2>
      <button
        onClick={() => reset()}
        className="mt-4 px-4 py-2 bg-blue-600 text-white rounded"
      >
        Try again
      </button>
    </div>
  );
}
```

---

## 4.2 — Server Actions and Mutations

```ts
// Write the server action here
"use server";

import { z } from "zod";
import { revalidatePath } from "next/cache";

const ProductSchema = z.object({
  name: z.string().min(3, "Name is too short"),
  price: z.number().positive("Price must be positive"),
});

// Discriminated Union for typed responses
type ActionResponse = 
  | { status: "success"; message: string }
  | { status: "error"; errors: string[] };

export async function createProduct(formData: FormData): Promise<ActionResponse> {
  const validatedFields = ProductSchema.safeParse({
    name: formData.get("name"),
    price: Number(formData.get("price")),
  });

  if (!validatedFields.success) {
    return {
      status: "error",
      errors: validatedFields.error.flatten().formErrors,
    };
  }

  try {
    // Mock DB Call
    console.log("Inserting into DB:", validatedFields.data);
    
    // Revalidate the products path
    revalidatePath("/products");
    
    return { status: "success", message: "Product created!" };
  } catch (e) {
    return { status: "error", errors: ["Database connection failed"] };
  }
}
```

```tsx
// Write the client form here
"use client";

import { useState } from "react";
import { createProduct } from "./actions";

export function ProductForm() {
  const [message, setMessage] = useState<string>("");

  async function handleSubmit(formData: FormData) {
    const result = await createProduct(formData);

    // Narrowing the type without assertions
    if (result.status === "error") {
      setMessage(`Error: ${result.errors.join(", ")}`);
    } else {
      setMessage(result.message);
      // Optional: reset form
    }
  }

  return (
    <form action={handleSubmit} className="flex flex-col gap-4 max-w-sm">
      <input name="name" placeholder="Product Name" className="border p-2" />
      <input name="price" type="number" placeholder="Price" className="border p-2" />
      <button type="submit" className="bg-black text-white p-2">Create</button>
      {message && <p>{message}</p>}
    </form>
  );
}
```

---

## 4.3 — Middleware

```ts
// Write middleware.ts here
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/request';

export function middleware(request: NextRequest) {
  const requestId = crypto.randomUUID();
  const response = NextResponse.next();

  // 1. Protected Route Check
  const token = request.cookies.get('auth-token');
  if (request.nextUrl.pathname.startsWith('/dashboard') && !token) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  // 2. Add Tracing Header
  response.headers.set('x-request-id', requestId);

  // 3. Basic Rate Limiting (Conceptual)
  const count = Number(request.cookies.get('rate-limit-count')?.value || 0);
  if (count > 100) {
    return new NextResponse('Too Many Requests', { status: 429 });
  }
  response.cookies.set('rate-limit-count', String(count + 1), { maxAge: 60 });

  return response;
}

export const config = {
  matcher: ['/dashboard/:path*', '/api/:path*'],
};
```

> Rate limiting explanation: In serverless/edge environments, each request may hit a different isolated instance or "node," meaning a local variable or basic cookie counter is easily bypassed and lacks a global "source of truth." Effective rate limiting in these environments requires a centralized, low-latency store like Redis (e.g., Upstash) to synchronize counts across all global edge locations.
