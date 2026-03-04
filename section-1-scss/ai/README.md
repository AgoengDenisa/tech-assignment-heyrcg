# Section 1 — SCSS (AI Assisted)

**This is Pass 2.** You may use AI tools to complete or improve your answers.

- **Permitted model:** Claude Sonnet only
- **Other AI tools (ChatGPT, Gemini, Copilot, etc.) are not permitted**

Refer to [../README.md](../README.md) for the full questions and shared tokens.

---

## 1.1 — Mixins and Functions

```scss
// Write your mixin and function here
@function grid-width($columns, $gutter) {
  // Formula: (100% - (Total Gutter Space)) / Number of Columns
  $gutter-total: $gutter * ($columns - 1);
  $width: (100% - $gutter-total) / $columns;
  
  // Rounding to 4 decimal places
  @return calc(round($width * 10000) / 10000);
}

@mixin responsive-grid($columns, $gutter, $breakpoint: md) {
  display: grid;
  gap: $gutter;
  grid-template-columns: repeat(1, 1fr);

  // Resolve breakpoint: check if it's a key in the map, otherwise use raw value
  $bp-value: if(map-has-key(map-get($tokens, breakpoints), $breakpoint), 
                map-get(map-get($tokens, breakpoints), $breakpoint), 
                $breakpoint);

  @media (min-width: $bp-value) {
    grid-template-columns: repeat($columns, 1fr);
  }
}
```

---

## 1.2 — Architecture and Nesting

**Problems identified:**
> 1. Deep nesting
> 2. Generic Tag
> 3. Magic numbers/ hardcoded colors
> 4. Redundant Logic

**Rewritten SCSS:**
```scss
// Write your improved SCSS here
// Pulling from your $tokens map
$primary-color: map-get(map-get($tokens, colors), primary);
$danger-color: map-get(map-get($tokens, colors), danger);

.page {
  background: $danger-color;

  &__content {
    // Replaces the generic 'div'
  }

  &__title {
    font-size: 1.875rem; // 30px
    color: $danger-color;

    &--is-active {
      color: #008000; // Example green
    }
  }

  &__subtitle {
    // Replaces the nested span
    font-size: 0.75rem; // 12px
  }

  &__link {
    // Replaces nested 'a'
    color: $primary-color;
    transition: opacity 0.2s;

    &:hover {
      opacity: 0.8;
    }
  }
}
```
