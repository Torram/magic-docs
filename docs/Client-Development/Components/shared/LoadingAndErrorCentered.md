---
sidebar_position: 3
---

This component generates a **[loading indicator](./loadingIndicator)**, and an **[error indicator](./errorindicator)** when needed, inside a **[centered div](./contentcenterdiv)**,

```tsx
import LoadingIndicator from './LoadingIndicator';
import ErrorIndicator from './ErrorIndicator';
import ContentCenterDiv from './ContentCenterDiv';

export default function LoadingAndErrorCentered({
    isLoading,
    isError,
    error
}: {
    isLoading: boolean;
    isError: boolean;
    error: any;
}): JSX.Element {
    return (
        <ContentCenterDiv>
            <LoadingIndicator isLoading={isLoading} />
            <ErrorIndicator isError={isError} error={error} />
        </ContentCenterDiv>
    );
}
```