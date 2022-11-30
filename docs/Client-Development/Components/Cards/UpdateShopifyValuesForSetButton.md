---
sidebar_position: 11
---

Similar to previous component, this component creates a button that sets the values of cards in shopify 'El Bazaar TCG' shop for cards in the current set.

## Functions

### UpdateShopifyValuesForSetButton

The function receives a setID string, and calls the hook _useUpdateShopifyValuesForSet_, with the setID as parameter.
Function returns a button element that updates quantities to shopify inventory.

```tsx
import ErrorIndicator from '../shared/ErrorIndicator';
import LoadingButton from '../shared/LoadingButton';
import { useUpdateShopifyValuesForSet } from '../../hooks/Sets/useUpdateShopifyValuesForSet';

export default function UpdateShopifyValuesForSetButton({ setID }: { setID: string }): JSX.Element {
    const { isError, error, isLoading, mutate } = useUpdateShopifyValuesForSet(setID);
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