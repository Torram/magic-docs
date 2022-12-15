Component that updates the list of all sets in the database, to add new sets.

```tsx
import React from 'react';
import LoadingButton from '../shared/LoadingButton';
import ErrorIndicator from '../shared/ErrorIndicator';
import { useUpdateSets } from '../../hooks/Sets/useUpdateSets';
import ContentCenterDiv from '../shared/ContentCenterDiv';

export default function UpdateSetsButton(): JSX.Element {
    const { isLoading, isError, error, mutate } = useUpdateSets();

    return (
        <ContentCenterDiv>
            <ErrorIndicator isError={isError} error={error} />
            <LoadingButton loading={isLoading} onClick={mutate}>
                Actualizar Sets
            </LoadingButton>
        </ContentCenterDiv>
    );
}
```