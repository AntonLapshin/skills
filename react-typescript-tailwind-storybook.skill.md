# SKILL: React + TypeScript + TailwindCSS + Storybook

Use for React projects with this tech stack.

## Requirements

- **React**: Functional components with hooks only
- **TypeScript**: Strict mode, explicit prop types
- **TailwindCSS**: Utility-first styling, design tokens in `tailwind.config.js`
- **Storybook**: Every visual component has stories

## Example: Component

```tsx
// components/Button/Button.tsx
export interface ButtonProps {
  variant: 'primary' | 'secondary';
  size: 'sm' | 'md' | 'lg';
  children: React.ReactNode;
  onClick?: () => void;
  disabled?: boolean;
}

export const Button = ({ variant, size, children, onClick, disabled = false }: ButtonProps) => {
  const base = 'rounded font-medium transition-colors';
  const variants = { primary: 'bg-blue-600 text-white', secondary: 'bg-gray-200 text-gray-800' };
  const sizes = { sm: 'px-2 py-1 text-sm', md: 'px-4 py-2 text-base', lg: 'px-6 py-3 text-lg' };

  return (
    <button className={`${base} ${variants[variant]} ${sizes[size]}`} onClick={onClick} disabled={disabled}>
      {children}
    </button>
  );
};
```

## Example: Story

```tsx
// Button.stories.tsx
export const Primary = () => <Button variant="primary" size="md">Click</Button>;
export const Secondary = () => <Button variant="secondary" size="md">Click</Button>;
```

## Critical Rules

- TypeScript strict mode always
- Tailwind utility classes directly in JSX (no CSS-in-JS)
- Every visual component has stories
- Follow atomic design: atoms → molecules → organisms
