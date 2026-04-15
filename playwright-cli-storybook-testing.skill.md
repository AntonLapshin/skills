# SKILL: Playwright CLI + Storybook Testing

## Context

Use this SKILL when testing new frontend features or UI components. Manually verify functionality by launching Storybook and interacting with stories using Playwright CLI (see `playwright-cli.skill.md` for CLI reference).

## Requirements

- Start Storybook before testing (`npm run storybook`)
- Use `playwright-cli` to open, navigate, and interact with stories
- Test components by simulating user interactions (clicks, typing, navigation)
- Verify UI states visually and functionally
- Document test scenarios and expected outcomes

## Testing Workflow

### 1. Start Storybook

```bash
# Terminal 1: Start Storybook
npm run storybook
# Wait for: "Storybook X.x.x started at http://localhost:6006"
```

### 2. Navigate to Specific Story

```bash
# Terminal 2: Open Playwright CLI at specific story
npx playwright open "http://localhost:6006/?path=/story/atoms-button--primary"
```

### 3. Interactive Testing Checklist

**Visual Verification:**
- [ ] Component renders correctly at different viewport sizes
- [ ] Colors, spacing, typography match design
- [ ] No console errors or warnings
- [ ] Responsive behavior works

**Interaction Testing:**
- [ ] Click interactions trigger expected behavior
- [ ] Form inputs accept and validate data
- [ ] Hover/focus states render correctly
- [ ] Keyboard navigation works (Tab, Enter, Escape)

**Edge Cases:**
- [ ] Empty states display correctly
- [ ] Loading states render properly
- [ ] Error states are handled gracefully
- [ ] Long content doesn't break layout

### 4. Example Test Session

Testing a RecipeCard component:

```bash
# Step 1: Start Storybook
npm run storybook

# Step 2: In another terminal, open the specific story
playwright-cli open "http://localhost:6006/?path=/story/organisms-recipecard--with-data"

# Step 3: Interact in the Playwright CLI:
# - Resize viewport to test responsive design
# - Click on card to test interaction
# - Hover over elements to check hover states
# - Use DevTools to inspect elements if needed

# Step 4: Test edge case - empty state
playwright-cli open "http://localhost:6006/?path=/story/organisms-recipecard--empty"

# Step 5: Test error state
playwright-cli open "http://localhost:6006/?path=/story/organisms-recipecard--error"
```

### 5. Generating Test Code from Session

Use `playwright-cli` to capture interactions. Navigate to story, interact, and generate test code:
```bash
playwright-cli open "http://localhost:6006/?path=/story/organisms-recipecard--with-data"
# ... interact with the component ...
# See playwright-cli.skill.md for test generation options
```

This generates code like:
```typescript
import { test, expect } from '@playwright/test';

test('test', async ({ page }) => {
  await page.goto('http://localhost:6006/?path=/story/organisms-recipecard--with-data');
  await page.getByRole('heading', { name: 'Pasta Carbonara' }).click();
  await page.getByText('20 min').click();
});
```

Save this to `tests/component.spec.ts` for automated regression testing.

## Story URL Format

```
http://localhost:6006/?path=/story/{category}-{component}--{story-name}
```

Examples:
- `atoms-button--primary` → `/story/atoms-button--primary`
- `organisms-recipe-list--loading` → `/story/organisms-recipe-list--loading`
- `pages-dashboard--default` → `/story/pages-dashboard--default`

## Viewport Testing

Test components at different screen sizes using playwright-cli:

```bash
# Mobile
playwright-cli open "http://localhost:6006/?path=/story/organisms-recipecard--with-data"
playwright-cli resize 375 667

# Tablet
playwright-cli resize 768 1024

# Desktop
playwright-cli resize 1920 1080
```

## Automation Integration

Use playwright-cli to interact with stories, then save the generated test code:

```bash
# 1. Open story and interact (generates test code)
playwright-cli open "http://localhost:6006/?path=/story/atoms-button--primary"
# Interact: click, type, etc.
# Copy generated code from the output

# 2. Save to test file
# See playwright-cli.skill.md for test generation details
```

## Testing With Mocked Side Effects

When stories use `ContextSideEffectsProvider`, all side effects are already mocked. Test as if API is real:

```bash
# This story has mocked getRecipes, so data appears instantly
playwright-cli open "http://localhost:6006/?path=/story/organisms-recipelist--with-data"

# Test loading state
playwright-cli open "http://localhost:6006/?path=/story/organisms-recipelist--loading"

# Test error state  
playwright-cli open "http://localhost:6006/?path=/story/organisms-recipelist--error"
```

## Screenshots for Documentation

```bash
# Screenshot each state for documentation
playwright-cli open "http://localhost:6006/?path=/story/atoms-button--primary"
playwright-cli screenshot --filename=docs/button-primary.png

# Or snapshot for detailed state capture
playwright-cli snapshot --filename=docs/button-snapshot.yml
```

## Critical Rules

- **Always start Storybook first** before running Playwright CLI
- **Test every story** when implementing or modifying a component
- **Test all states**: default, loading, empty, error, edge cases
- **Use playwright-cli** to capture complex interactions for regression tests
- **Test responsive behavior** at multiple viewport sizes
- **Verify side effect mocks** work correctly in stories
- **Screenshot key states** for documentation and visual regression
- **Document test scenarios** - what was tested, what passed/failed
