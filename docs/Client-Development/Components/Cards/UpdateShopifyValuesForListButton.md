---
sidebar_position: 10
---

This component creates a button that sets the values of cards in the shopify 'El Bazaar TCG' e-shop for the cards in the current list (binder).

## Functions

### UpdateShopifyValuesForListButton

The function receives a listID string, and calls the hook _useUpdateShopifyValuesForList_, with the listID as parameter.
Function returns a button that updates the quantities to the shopify inventory.

```tsx
import ErrorIndicator from '../shared/ErrorIndicator';
import LoadingButton from '../shared/LoadingButton';
import { useUpdateShopifyValuesForList } from '../../hooks/Lists/useUpdateShopifyValuesForList';

export default function UpdateShopifyValuesForListButton({ listID }: { listID: string }): JSX.Element {
    const { isError, error, isLoading, mutate } = useUpdateShopifyValuesForList(listID);
    return (
        <div className={'w-100'}>
            <ErrorIndicator isError={isError} error={error} />
            <LoadingButton className={'w-100'} variant={'outline-secondary'} loading={isLoading} onClick={mutate}>
                Actualizar a Shopify
            </LoadingButton>
        </div>
    );
}
```