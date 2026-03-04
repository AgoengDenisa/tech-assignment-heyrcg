# Section 3 — React and TypeScript (AI Assisted)

**This is Pass 2.** You may use AI tools to complete or improve your answers.

- **Permitted model:** Claude Sonnet only
- **Other AI tools (ChatGPT, Gemini, Copilot, etc.) are not permitted**

Refer to [../README.md](../README.md) for the full questions.

---

## 3.1 — Custom Hook

```ts
// Write your hook + usage example here
function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      // Only update if the value actually changed to avoid unnecessary renders
      setDebouncedValue((prev) => (prev === value ? prev : value));
    }, delay);

    // Cleanup: clears the timeout if value or delay changes, or on unmount
    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);

  return debouncedValue;
}
const SearchComponent = () => {
  const [search, setSearch] = useState("");
  const debouncedSearch = useDebounce<string>(search, 500);

  useEffect(() => {
    if (debouncedSearch) {
      // Trigger API call
      console.log(`Fetching results for: ${debouncedSearch}`);
    }
  }, [debouncedSearch]);

  return (
    <input 
      type="text" 
      value={search} 
      onChange={(e) => setSearch(e.target.value)} 
      placeholder="Search..." 
    />
  );
};
```

---

## 3.2 — Component Design

```tsx
// Write types + component implementation here

```
