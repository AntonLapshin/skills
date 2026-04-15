# SKILL: Design System with Atom Components

Use when creating new UI components. Start with atoms to build a reusable design system.

## Requirements

- Atomic design: Atoms → Molecules → Organisms → Templates → Pages
- Folder: `components/<Category>/<ComponentName>/`
- Every component must have stories
- Reuse atoms before creating new ones

## Folder Structure

```
components/
├── atoms/Button/
│   ├── Button.tsx
│   ├── Button.stories.tsx
│   └── index.ts
├── molecules/FormField/
├── organisms/RecipeCard/
└── templates/MainLayout/
```

## Example: Atom Component

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

export const Badge = ({ children, variant }: BadgeProps) => (
  <span className={`px-2 py-1 rounded-full text-sm font-medium ${variantClasses[variant]}`}>
    {children}
  </span>
);
```

```tsx
// Badge.stories.tsx
export const Success = () => <Badge variant="success">Active</Badge>;
export const Warning = () => <Badge variant="warning">Pending</Badge>;
```

```tsx
// index.ts
export { Badge, type BadgeProps, type BadgeVariant } from './Badge';
```

## Example: Molecule (Composed Atoms)

```tsx
// components/molecules/FormField/FormField.tsx
import { Label } from '../../atoms/Label';
import { Input, InputProps } from '../../atoms/Input';

export interface FormFieldProps extends InputProps {
  label: string;
  error?: string;
}

export const FormField = ({ label, error, ...inputProps }: FormFieldProps) => (
  <div className="space-y-1">
    <Label>{label}</Label>
    <Input {...inputProps} error={!!error} />
    {error && <span className="text-red-500 text-sm">{error}</span>}
  </div>
);
```

## Critical Rules

- Check existing atoms before creating new components
- New component = new story file (no exceptions)
- Export from `index.ts` for clean imports
