# UI Development Guidelines

Strict standards derived from project skills for building UI, components, Storybook stories, and tests.

---

## Component Architecture

### Atomic Design Hierarchy
- **atoms/**: Basic building blocks (Button, Input, Label, Icon)
- **molecules/**: Composed atoms (FormField, SearchBar)
- **organisms/**: Complex components (RecipeCard, RecipeList)
- **templates/**: Page layouts (MainLayout)
- **pages/**: Full page components

### Folder Structure
```
components/<Category>/<ComponentName>/
  ├── <ComponentName>.tsx       # Component implementation
  ├── <ComponentName>.stories.tsx # Storybook stories (required)
  ├── <ComponentName>.test.tsx    # Component tests (if needed)
  └── index.ts                    # Clean exports
```

### Component Code Standards
- **TypeScript**: Strict mode, explicit interfaces for props
- **Functional components** with hooks only
- **Props naming**: Descriptive, use `variant` for visual variants, `size` for sizing
- **Default props**: Inline in destructuring (e.g., `disabled = false`)
- **TailwindCSS**: Utility classes directly in JSX, use Record<Variant, string> for variant maps

### Export Pattern
```ts
// index.ts
export { ComponentName, type ComponentNameProps, type ComponentNameVariant } from './ComponentName';
```

---

## Styling (TailwindCSS)

- Utility-first only - no CSS-in-JS
- Use `tailwind.config.js` for design tokens
- Variant classes via Record mapping: `variantClasses[variant]`
- Size classes via Record mapping: `sizeClasses[size]`
- Combine with template literals: `` `${base} ${variantClasses[variant]}` ``

---

## Side Effects Pattern (Mandatory)

Every side effect (API, external integration) **MUST** follow this pattern:

### 1. Contract (Interface)
```ts
// sideEffects/getData.ts
export const getData = async (params: Params): Promise<Result> => {
  throw new Error('Not implemented');
};
```

### 2. Mock Implementation
```ts
// sideEffects/getData.mock.ts
export const getDataMock = async (params: Params): Promise<Result> => {
  return [/* realistic mock data */];
};
```

### 3. Production Implementation
```ts
// sideEffects/getData.prod.ts
export const getData = async (params: Params): Promise<Result> => {
  // Real API call
};
```

### 4. Context Setup
```tsx
// contexts/ContextSideEffects.tsx
export interface SideEffects {
  getData: typeof getData;
}

const defaultSideEffects: SideEffects = {
  getData: getDataProd,
};

export const ContextSideEffects = createContext<SideEffects>(defaultSideEffects);
```

### 5. Component Usage
```tsx
const { getData } = useContext(ContextSideEffects);
```

**Rule**: Never call side effects directly - always through context.

---

## Storybook Stories

### Mandatory Requirements
- **Every visual component must have stories** - no exceptions
- File naming: `<ComponentName>.stories.tsx` alongside component

### Atom/Molecule Stories
```tsx
export const Primary = () => <Button variant="primary">Text</Button>;
export const Secondary = () => <Button variant="secondary">Text</Button>;
```

### Organism/Page Stories (with Side Effects)
Wrap with `ContextSideEffectsProvider` and provide mock implementations:

```tsx
export const Loading = () => (
  <ContextSideEffectsProvider value={{ getData: () => new Promise(() => {}) }}>
    <RecipeList />
  </ContextSideEffectsProvider>
);

export const WithData = () => (
  <ContextSideEffectsProvider value={{ getData: async () => mockData }}>
    <RecipeList />
  </ContextSideEffectsProvider>
);

export const Empty = () => (
  <ContextSideEffectsProvider value={{ getData: async () => [] }}>
    <RecipeList />
  </ContextSideEffectsProvider>
);

export const Error = () => (
  <ContextSideEffectsProvider value={{ getData: async () => { throw new Error('fail'); } }}>
    <RecipeList />
  </ContextSideEffectsProvider>
);
```

### Required Story States
- Default/Primary state
- Loading state (never-resolving promise)
- Empty state (empty arrays)
- Error state (thrown errors)
- Edge cases (long content, max items)

---

## Testing

### Unit Tests: Pure Functions
Extract all logic into pure functions and test separately:

```ts
// utils/calculate.ts
export const calculateValue = (a: number, b: number): number => {
  if (b <= 0) throw new Error('Invalid');
  return a / b;
};

// utils/calculate.test.ts - alongside implementation
import { describe, it, expect } from 'vitest';
import { calculateValue } from './calculate';

describe('calculateValue', () => {
  it('calculates correctly', () => {
    expect(calculateValue(10, 2)).toBe(5);
  });

  it('throws on invalid input', () => {
    expect(() => calculateValue(10, 0)).toThrow();
  });
});
```

### Component Testing (Playwright + Storybook)
Use `playwright-cli` to test stories interactively:

```bash
# 1. Start Storybook
npm run storybook

# 2. Test specific story
npx playwright-cli open "http://localhost:6006/?path=/story/atoms-button--primary"

# 3. Resize, click, verify
```

### Testing Checklist
- [ ] Visual: Renders correctly at all viewport sizes
- [ ] Visual: Colors, spacing match design
- [ ] Visual: No console errors
- [ ] Interaction: Click triggers expected behavior
- [ ] Interaction: Keyboard navigation works (Tab, Enter, Escape)
- [ ] Edge Cases: Empty states
- [ ] Edge Cases: Loading states
- [ ] Edge Cases: Error states handled gracefully
- [ ] Edge Cases: Long content doesn't break layout

---

## Code Quality Rules

1. **Check existing atoms** before creating new components
2. **No component without a story**
3. **No side effect without context**
4. **No logic in components** - extract to pure functions
5. **Index.ts exports** for all components
6. **TypeScript strict** - no `any`, explicit return types
