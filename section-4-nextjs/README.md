# Section 4 — Next.js (App Router)

## 4.1 — Data Fetching and Caching

You are building a product listing page.

Explain (with code) how you would:

1. Create a **Server Component** that fetches a list of products from an external REST API
2. Implement **ISR** with a 60-second revalidation window
3. Add a **loading state** using the App Router's file-based conventions
4. Handle errors gracefully using the App Router's error boundary conventions

**Additional Requirement**

- You must demonstrate correct use of Next.js caching/revalidation semantics (e.g. `fetch(..., { next: { revalidate } })` or route segment revalidation).

Provide the relevant file structure and code for:

- `page.tsx`
- `loading.tsx`
- `error.tsx`

```txt
// Include file structure here
```

```tsx
// Include page.tsx here
export const getStaticProps: GetStaticProps<Props> = async ({
  params,
}: {
  params: { id: string };
}) => {
  try {
    const post = await fetch(`https://api.vercel.app/blog/${params.id}`).then(
      (res) => res.json(),
    );

    return {
      props: { post },
      // Next.js will invalidate the cache when a
      // request comes in, at most once every 60 seconds.
      revalidate: 60,
    };
  } catch (e) {
    return router.push("/404");
  }
};

export default function Page({ post }: Props) {
  return (
    <main>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </main>
  );
}
```

```tsx
// Include loading.tsx here
export default function Loading() {
  // You can add any UI inside Loading, including a Skeleton.
  return <div>Loading dashboard content...</div>;
}
```

```tsx
// Include error.tsx here
export default function Loading() {
  // You can add any UI inside Loading, including a Skeleton.
  return <div>Loading dashboard content...</div>;
}
```

---

## 4.2 — Server Actions and Mutations

Write a Next.js Server Action that handles a form submission for creating a new product.

The action must:

- Validate the input using `zod`
- Insert the product into a database (you may mock the DB call)
- Revalidate the `/products` path after successful insertion
- Return typed success/error responses

**Additional Requirements**

- The return type must be a discriminated union
- The client must narrow the type without assertions

Show how this action is consumed from a Client Component form.

```ts
// Write the server action here
import * as z from "zod";
const Player = z.object({
  username: z.string(),
  xp: z.number(),
});
try {
  Player.parse({ username: 42, xp: "100" });
} catch (error) {
  if (error instanceof z.ZodError) {
    error.issues;
  }
}
```

```tsx
// Write the client form here
```

---

## 4.3 — Middleware

Write a `middleware.ts` file that:

1. Redirects unauthenticated users to `/login` for any route under `/dashboard/*`
2. Adds a custom `x-request-id` header (UUID) to every response for tracing
3. Implements a basic rate-limiting check using a header or cookie-based counter (conceptual implementation is fine)

**Additional Requirement**

- Include 1–2 sentences explaining why this rate limiting approach is imperfect in serverless/edge environments.

```ts
// Write middleware.ts here
export function middleware(request: NextRequest) {
  // Example: Check if user is authenticated
  const isAuthenticated = request.header.has('x-request-id'); 

  // If not authenticated and trying to access a protected path, redirect
  if (!isAuthenticated && request.nextUrl.pathname.startsWith('/dashboard/*')) {
    return NextResponse.redirect(new URL('/login', request.url));
  }
  
  // Continue to the requested route
  return NextResponse.next();
}

// Optionally, configure which paths the middleware should run on
export const config = {
  matcher: [
    '/((?!api|_next/static|_next/image|favicon.ico).*)', // Run on all paths except API routes, static files, and favicon
  ],
};
```

> Rate limiting explanation: since it use serverless/edge the bandwidth of the service itself already maintain by the infra owner (vercel/render), which is useless if we try to put rate limitter by manual.
