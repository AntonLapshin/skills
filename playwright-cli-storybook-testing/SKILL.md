---
name: testing-storybook-playwright
description: Test frontend components interactively by combining Storybook with Playwright CLI. Use when verifying visual components, testing responsive behavior, or validating interaction states in Storybook stories.
---

# Playwright CLI + Storybook Testing

## Requirements

- Start Storybook first (`npm run storybook`)
- Open stories with `npx playwright open <story-url>`
- Simulate clicks, typing, navigation
- Test all states: loading, success, error, empty

## Workflow

```bash
# Terminal 1: Start Storybook
npm run storybook

# Terminal 2: Open specific story
npx playwright open "http://localhost:6006/?path=/story/atoms-button--primary"

# Resize viewport
playwright-cli resize 375 667

# Take screenshot
playwright-cli screenshot --filename=button.png
```

## Story URL Format

```
http://localhost:6006/?path=/story/{category}-{component}--{story-name}
```

Examples:
- `/story/atoms-button--primary`
- `/story/organisms-recipe-list--loading`

## Testing Checklist

**Visual:**
- [ ] Renders at all viewport sizes
- [ ] Colors, spacing match design
- [ ] No console errors
- [ ] Responsive behavior works

**Interaction:**
- [ ] Click triggers expected behavior
- [ ] Form inputs accept/validate data
- [ ] Hover/focus states render
- [ ] Keyboard navigation (Tab, Enter, Escape)

**Edge Cases:**
- [ ] Empty states display
- [ ] Loading states render
- [ ] Error states handled
- [ ] Long content doesn't break layout

## Example Session

```bash
# 1. Start Storybook
npm run storybook

# 2. Test data state
playwright-cli open "http://localhost:6006/?path=/story/organisms-recipecard--with-data"

# 3. Test empty state
playwright-cli open "http://localhost:6006/?path=/story/organisms-recipecard--empty"

# 4. Test error state
playwright-cli open "http://localhost:6006/?path=/story/organisms-recipecard--error"
```

## Critical Rules

- Start Storybook before Playwright CLI
- Test every story and every state
- Test responsive behavior at multiple sizes
- Document what passed/failed
