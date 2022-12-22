---
sidebar_position: 9
---

This component creates a button that calls a function to update the prices of all cards of a set.

## Function

### UpdatePriceForCardsInSetButton

The function receives a setID string, and calls the hook _useUpdatePricesForCardsInSet_ with that setID as parameter. The function returns a button that updates the prices of all cards in the provided set.

```tsx
import { useUpdatePricesForCardsInSet } from '../../hooks/Cards/useUpdatePricesForCardsInSet';
import ErrorIndicator from '../shared/ErrorIndicator';
import LoadingButton from '../shared/LoadingButton';

export default function UpdatePricesForCardsInSetButton({ setID }: { setID: string }): JSX.Element {
    const { isError, error, isLoading, mutate } = useUpdatePricesForCardsInSet(setID);
    return (
        <div className={'w-100'}>
            <ErrorIndicator isError={isError} error={error} />
            <LoadingButton className={'w-100'} variant={'outline-secondary'} loading={isLoading} onClick={mutate}>
                Actualizar precios
            </LoadingButton>
        </div>
    );
}
```