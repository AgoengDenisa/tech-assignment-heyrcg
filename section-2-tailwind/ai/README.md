# Section 2 — Tailwind CSS (AI Assisted)

**This is Pass 2.** You may use AI tools to complete or improve your answers.

- **Permitted model:** Claude Sonnet only
- **Other AI tools (ChatGPT, Gemini, Copilot, etc.) are not permitted**

Refer to [../README.md](../README.md) for the full questions.

---

## 2.1 — Utility Composition

```tsx
// Write JSX/HTML here
<div className="group w-full max-w-[400px] cursor-pointer overflow-hidden rounded-xl border border-slate-200 bg-white transition-all duration-300 hover:-translate-y-1 hover:shadow-lg dark:border-slate-700 dark:bg-slate-900">
  {/* Manual 16:9 Aspect Ratio Container */}
  <div className="relative w-full pb-[56.25%]">
    <img
      src="https://images.unsplash.com/photo-1618005182384-a83a8bd57fbe"
      alt="Card Header"
      className="absolute inset-0 h-full w-full object-cover"
    />
  </div>

  {/* Content Section */}
  <div className="p-6">
    <h3 className="mb-2 text-xl font-bold text-slate-900 dark:text-white">
      Tailwind Architecture
    </h3>
    <p className="mb-6 text-sm leading-relaxed text-slate-600 dark:text-slate-400">
      Master utility-first CSS by composing atomic classes into robust, responsive components that support both light and dark modes out of the box.
    </p>
    
    <button className="w-full rounded-lg bg-blue-600 px-4 py-2 text-sm font-semibold text-white transition-colors hover:bg-blue-700 focus:outline-none focus-visible:ring-2 focus-visible:ring-blue-500 focus-visible:ring-offset-2 dark:focus-visible:ring-offset-slate-900">
      Get Started
    </button>
  </div>
</div>
```

---

## 2.2 — Tailwind Configuration

```ts
// Write your tailwind.config.ts snippet here
import type { Config } from 'tailwindcss';
import plugin from 'tailwindcss/plugin';

const config: Config = {
  // 4. Content paths for Next.js App Router
  content: [
    './app/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  darkMode: 'class',
  theme: {
    extend: {
      // 1. Custom 'brand' color palette
      colors: {
        brand: {
          50: '#f0f9ff',
          100: '#e0f2fe',
          200: '#bae6fd',
          300: '#7dd3fc',
          400: '#38bdf8',
          500: '#0ea5e9',
          600: '#0284c7',
          700: '#0369a1',
          800: '#075985',
          900: '#0c4a6e',
        },
      },
      // 2. Custom fontFamily with fallback
      fontFamily: {
        display: ['var(--font-display)', 'sans-serif'],
      },
    },
  },
  plugins: [
    // 3 & 5. Typed plugin for .text-balance
    plugin(({ addUtilities }) => {
      addUtilities({
        '.text-balance': {
          'text-wrap': 'balance',
        },
      });
    }),
  ],
};

export default config;
```
