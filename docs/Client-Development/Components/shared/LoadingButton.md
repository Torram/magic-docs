---
sidebar_position: 4
---

This component generates a generic button with a spinner indicator inside.

```tsx
import Button from 'react-bootstrap/Button';
import Spinner from 'react-bootstrap/Spinner';

export default function LoadingButton({
    onClick,
    className,
    variant,
    type,
    disabled,
    loading,
    children
}: {
    onClick?: any;
    className?: string;
    variant?: string;
    type?: string;
    disabled?: boolean;
    loading: boolean;
    children: any;
}): JSX.Element {
    return (
        <Button onClick={onClick} className={className} variant={variant} type={type} disabled={disabled}>
            {loading ? <Spinner as='span' animation='grow' size='sm' role='status' aria-hidden='true' /> : children}
        </Button>
    );
}
```