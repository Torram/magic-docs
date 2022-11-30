---
sidebar_position: 2
---

Component that generates a button that updates all of the inventory from shopify.


```tsx
import React from 'react';
import LoadingButton from '../shared/LoadingButton';
import ErrorIndicator from '../shared/ErrorIndicator';
import { useUpdateDatabaseFromShopify } from '../../hooks/Shopify/useUpdateDatabaseFromShopify';
import ContentCenterDiv from '../shared/ContentCenterDiv';

export default function UpdateDatabaseFromShopifyButton(): JSX.Element {
    const { isLoading, isError, error, mutate } = useUpdateDatabaseFromShopify();

    return (
        <ContentCenterDiv>
            <ErrorIndicator isError={isError} error={error} />
            <LoadingButton loading={isLoading} onClick={mutate}>
                Actualizar desde Shopify
            </LoadingButton>
        </ContentCenterDiv>
    );
}
```