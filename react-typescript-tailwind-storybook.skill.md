# SKILL: React + TypeScript + TailwindCSS + Storybook

## Context

Use this SKILL when creating a new React frontend project or working on an existing one with this tech stack.

## Requirements

- **React**: Use functional components with hooks
- **TypeScript**: Strict mode enabled, explicit types for props and return values
- **TailwindCSS**: Utility-first styling, consistent design tokens via `tailwind.config.js`
- **Storybook**: All visual components must have corresponding stories

## Examples

### Component Structure

```tsx
// components/Button/Button.tsx
export interface ButtonProps {
  variant: 'primary' | 'secondary';
  size: 'sm' | 'md' | 'lg';
  children: React.ReactNode;
  onClick?: () => void;
  disabled?: boolean;
}

export const Button = ({
  variant,
  size,
  children,
  onClick,
  disabled = false,
}: ButtonProps) => {
  const baseClasses = 'rounded font-medium transition-colors';
  const variantClasses = {
    primary: 'bg-blue-600 text-white hover:bg-blue-700',
    secondary: 'bg-gray-200 text-gray-800 hover:bg-gray-300',
  };
  const sizeClasses = {
    sm: 'px-2 py-1 text-sm',
    md: 'px-4 py-2 text-base',
    lg: 'px-6 py-3 text-lg',
  };

  return (
    <button
      className={`${baseClasses} ${variantClasses[variant]} ${sizeClasses[size]}`}
      onClick={onClick}
      disabled={disabled}
    >
      {children}
    </button>
  );
};
```

### Storybook Story

```tsx
// components/Button/Button.stories.tsx
import { Button } from './Button';

export const Primary = () => {
  return <Button variant="primary" size="md">Click me</Button>;
};

export const Secondary = () => {
  return <Button variant="secondary" size="md">Click me</Button>;
};
```

## Critical Rules

- Always use TypeScript strict mode
- Use Tailwind utility classes directly in components (no CSS-in-JS)
- Create Storybook stories for every visual component
- Follow atomic design principles (atoms → molecules → organisms)
