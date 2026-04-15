# SKILL: Design System with Atom Components

## Context

Use this SKILL when creating new UI components. Always start with atom components to build and maintain a reusable design system.

## Requirements

- Follow atomic design: Atoms → Molecules → Organisms → Templates → Pages
- New components must be created in the `components` folder with proper categorization
- Every component must have Storybook stories
- Reuse existing atoms before creating new ones
- Component folder structure: `components/<Category>/<ComponentName>/`

## Examples

### Folder Structure

```
components/
├── atoms/
│   ├── Button/
│   │   ├── Button.tsx
│   │   ├── Button.stories.tsx
│   │   ├── Button.test.tsx
│   │   └── index.ts
│   ├── Input/
│   ├── Label/
│   └── Icon/
├── molecules/
│   ├── FormField/
│   │   ├── FormField.tsx      # Composes Label + Input + Error
│   │   ├── FormField.stories.tsx
│   │   └── index.ts
│   └── SearchBar/
├── organisms/
│   ├── RecipeCard/
│   └── RecipeForm/
└── templates/
    └── MainLayout/
```

### Creating a New Atom Component

```tsx
// components/atoms/Badge/Badge.tsx
export type BadgeVariant = 'success' | 'warning' | 'error' | 'info';

export interface BadgeProps {
  children: React.ReactNode;
  variant: BadgeVariant;
}

const variantClasses: Record<BadgeVariant, string> = {
  success: 'bg-green-100 text-green-800',
  warning: 'bg-yellow-100 text-yellow-800',
  error: 'bg-red-100 text-red-800',
  info: 'bg-blue-100 text-blue-800',
};

export const Badge = ({ children, variant }: BadgeProps) => {
  return (
    <span className={`px-2 py-1 rounded-full text-sm font-medium ${variantClasses[variant]}`}>
      {children}
    </span>
  );
};
```

```tsx
// components/atoms/Badge/Badge.stories.tsx
import { Badge } from './Badge';

export const Success = () => <Badge variant="success">Active</Badge>;
export const Warning = () => <Badge variant="warning">Pending</Badge>;
export const Error = () => <Badge variant="error">Failed</Badge>;
export const Info = () => <Badge variant="info">New</Badge>;
```

```tsx
// components/atoms/Badge/index.ts
export { Badge, type BadgeProps, type BadgeVariant } from './Badge';
```

### Composing Atoms into Molecules

```tsx
// components/molecules/FormField/FormField.tsx
import { Label } from '../../atoms/Label';
import { Input, InputProps } from '../../atoms/Input';

export interface FormFieldProps extends InputProps {
  label: string;
  error?: string;
}

export const FormField = ({ label, error, ...inputProps }: FormFieldProps) => {
  return (
    <div className="space-y-1">
      <Label>{label}</Label>
      <Input {...inputProps} error={!!error} />
      {error && <span className="text-red-500 text-sm">{error}</span>}
    </div>
  );
};
```

## Critical Rules

- Always check if an existing atom can serve your need before creating a new component
- New component = new story file (no exceptions)
- Organize by atomic levels: `atoms/`, `molecules/`, `organisms/`, `templates/`
- Export from `index.ts` for clean imports
