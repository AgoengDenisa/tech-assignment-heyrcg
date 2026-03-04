# Section 1 — SCSS (No AI)

**This is Pass 1.** Complete your answers using only your own knowledge and official documentation. AI tools are not permitted during this pass. You may skip questions and return to them in Pass 2.

Refer to [../README.md](../README.md) for the full questions and shared tokens.

---

## 1.1 — Mixins and Functions

```scss
// Write your mixin and function here
@function getToken($directory){
    $results: $tokens
    @each $key in $path{
        $result: map-get($result, $key)
    @return $result
    }
}

@function width($columns, $row){
    $row-space: $columns * ($row - 1)
    $available-space: $100% - $row-space
    $col-width: $available-space /$columns
    @return round($col-width *1000) / 10
}
@mixin responsiveGrid($columns, $row, $breakpoint ){
    $break-point: if($breakpoint === null, getToken((breakpoint,md)), $breakpoint)
    @if type-of($break-point) == string{
        $break-point: getToken((breakpoint, $break-point))
    }
    display: grid
    grid-template-column: 1fr
    gap: $row
    @media(min-width: $break-point){
        grid-template-column: repeat($columns, 1fr)
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
