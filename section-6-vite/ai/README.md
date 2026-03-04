# Section 6 — Vite and Build Tooling (AI Assisted)

**This is Pass 2.** You may use AI tools to complete or improve your answers.

- **Permitted model:** Claude Sonnet only
- **Other AI tools (ChatGPT, Gemini, Copilot, etc.) are not permitted**

Refer to [../README.md](../README.md) for the full questions.

---

## 6.1 — Configuration

```ts
// Write vite.config.ts here
import { defineConfig, loadEnv } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig(({ mode }) => {
  // 4. Environment variable validation
  const env = loadEnv(mode, process.cwd(), '');
  if (!env.VITE_API_URL) {
    throw new Error('FATAL: VITE_API_URL is not defined in the environment.');
  }

  return {
    plugins: [react()],
    resolve: {
      // 1. Path aliases
      alias: {
        '@': path.resolve(__dirname, './src'),
      },
    },
    server: {
      // 2. Proxy configuration
      proxy: {
        '/api': {
          target: 'http://localhost:8000',
          changeOrigin: true,
          rewrite: (path) => path.replace(/^\/api/, ''),
        },
      },
    },
    build: {
      rollupOptions: {
        output: {
          // 3. Separate vendor chunks for React
          manualChunks: {
            'react-vendor': ['react', 'react-dom'],
          },
        },
      },
    },
  };
});
```

```json
// Write tsconfig.json snippet here
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  }
}
```

---

## 6.2 — Concepts

**1. How does Vite's dev server differ architecturally from Webpack's, and why is this faster?**
> Answer: Vite leverages Native ESM to serve source code, meaning the browser handles the "bundling" of modules on demand. Unlike Webpack, which must crawl and bundle your entire project before the server starts, Vite only processes the specific files requested by the browser, leading to near-instant startup times regardless of project size.

**2. What is the role of Rollup in a Vite production build, and when might you choose esbuild instead?**
> Answer: Vite uses Rollup for its production build because it offers a mature, highly optimized ecosystem for generating small, efficient CSS and JS bundles with advanced tree-shaking. While esbuild is used by Vite for ultra-fast dependency pre-bundling and TypeScript transpilation, you generally wouldn't swap Rollup for esbuild for the final production "bundling" yet, as esbuild's CSS handling and code-splitting are not yet as feature-complete as Rollup's.

**3. You notice that a third-party library is causing full-page reloads instead of HMR updates. What is the most likely cause, and how would you fix it?**
> Answer: This usually happens when a library contains side effects or exports that don't satisfy the "HMR Boundary" requirements, causing the HMR update to bubble up to the root. To fix this, you can try adding the library to optimizeDeps.include in vite.config.ts to ensure it is pre-bundled into a single ESM module, or check if the library needs a specific Vite plugin to handle its custom file types or internal state.
