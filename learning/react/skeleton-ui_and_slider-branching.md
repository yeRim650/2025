# 1. Skeleton UI Pattern

## Definition

A **loading UI pattern that shows a visual placeholder or layout structure before the actual content is loaded**.  
Instead of showing a loading spinner, it presents a mock layout to indicate that the application is responsive even when loading slowly.

---

## Example Implementation (React)

```jsx
return isLoading ? (
  <div className="skeleton-card" />
) : (
  <ActualCard data={data} />
)
```

---

## Example Libraries

- `react-loading-skeleton`
- `MUI Skeleton`
- Tailwind CSS example:

  ```html
  <div class="animate-pulse bg-gray-300 h-4 w-full rounded" />
  ```

---

## UX Benefits

| Loading Spinner | Skeleton UI             |
|-----------------|-------------------------|
| Shows only delay | Gives structural hint   |
| Can feel unresponsive | Perceived progress and layout awareness |

---

## Caveats

- If the skeleton display duration is too short, it can cause visual flicker
- Overuse of skeletons may reduce usability
- **If the skeleton layout differs too much from the final content, it can be confusing**

---

# 2. Slider Data Branching

## Definition

A pattern that **branches logic or fetches different data based on the current index of a slider (carousel, view pager, etc.)**.

---

## Common Use Cases

- Slider acts like tab navigation (e.g., News / Books / Courses)
- First slide shows summary; next slides show details
- Trigger different API calls based on slide index

---

## Example Implementation (React)

```jsx
const handleSlideChange = (index) => {
  switch (index) {
    case 0:
      setData(newsData);
      break;
    case 1:
      fetch('/api/books').then(...);
      break;
    case 2:
      setData([]);
      break;
  }
}
```

---

## Caveats

- **Lazy loading** may introduce lag when sliding → consider prefetching or caching
- Desynchronization may occur if slider state and data state are managed separately
- Too many branches can increase code complexity

---

# Combining Skeleton UI with Slider Branching

When changing slides, use skeleton placeholders within each slide while loading:

```jsx
{isLoading ? (
  <SkeletonSlide />
) : (
  <ActualSlide data={data[currentSlide]} />
)}
```

---

# Summary

| Pattern Name           | Purpose                          | Caveats                                         |
|------------------------|----------------------------------|------------------------------------------------|
| Skeleton UI            | Enhance UX by reducing load anxiety | Flicker, mismatched layout                      |
| Slider Data Branching  | Show or fetch data per view      | State sync issues, excessive branching, API overhead |
