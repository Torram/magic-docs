---
sidebar_position: 5
---

This component generates a animated loading indicator for general purpose.

```tsx
import Spinner from 'react-bootstrap/Spinner';

export default function LoadingIndicator({ isLoading, ...others }: { isLoading?: boolean }): JSX.Element | null {
    return isLoading != null && isLoading ? <Spinner {...others} animation={'grow'} /> : null;
}
```