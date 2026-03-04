# Section 4 — Next.js App Router (No AI)

**This is Pass 1.** Complete your answers using only your own knowledge and official documentation. AI tools are not permitted during this pass. You may skip questions and return to them in Pass 2.

Refer to [../README.md](../README.md) for the full questions.

---

## 4.1 — Data Fetching and Caching

```txt
// Include file structure here
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react-swc";
import path from "path";

export default defineConfig({
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "../components/*"),
      "@server": path.resolve(__dirname, "../server/src"),
    },
  },
  plugins: [react()],
  server: {
    proxy: {
        '/api': {
          target: 'https://localhost:8000',
          changeOrigin: true,
          secure: false,      
          ws: true,
          configure: (proxy, _options) => {
            proxy.on('error', (err, _req, _res) => {
              console.log('proxy error', err);
            });
            proxy.on('proxyReq', (proxyReq, req, _res) => {
              console.log('Sending Request to the Target:', req.method, req.url);
            });
            proxy.on('proxyRes', (proxyRes, req, _res) => {
              console.log('Received Response from the Target:', proxyRes.statusCode, req.url);
            });
          },
        }
      }
  },
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom'], 
          utils: ['./src/utils/utility-file.js', './src/helpers/helper-file.js'],
        }
      }
    }
  },
   define: {
      stage: process.env.NODE_ENV,
    },ß
});

```

```tsx
// Include page.tsx here
```

```tsx
// Include loading.tsx here
```

```tsx
// Include error.tsx here
```

---

## 4.2 — Server Actions and Mutations

```ts
// Write the server action here
```

```tsx
// Write the client form here
```

---

## 4.3 — Middleware

```ts
// Write middleware.ts here
```

> Rate limiting explanation:
