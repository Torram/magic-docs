---
sidebar_position: 2
---

This component generates a customized alert element for general purpose.

```tsx
import Alert from 'react-bootstrap/Alert';

export default function ErrorIndicator({
    isError,
    error,
    className,
    ...others
}: {
    isError: boolean;
    error: any;
    className?: string;
}): JSX.Element | null {
    return isError ? (
        <Alert {...others} className={`mb-3 ${className ?? 'default'}`} variant='danger'>
            {error.message}
        </Alert>
    ) : null;
}
```