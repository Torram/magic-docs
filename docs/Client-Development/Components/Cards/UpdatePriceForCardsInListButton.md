---
sidebar_position: 8
---

This component creates a button that calls a function to update the prices of all cards in a list (binder).

## Function

### UpdatePriceForCardsInListButton

The function receives a listID string, and calls the hook _useUpdatePricesForCardsInList_ with that listID as parameter. The function returns a button that updates the prices of all cards in the provided list.

```tsx
import ErrorIndicator from '../shared/ErrorIndicator';
import LoadingButton from '../shared/LoadingButton';
import { useUpdatePricesForCardsInList } from '../../hooks/Cards/useUpdatePricesForCardsInList';

export default function UpdatePriceForCardsInListButton({ listID }: { listID: string }): JSX.Element {
    const { isError, error, isLoading, mutate } = useUpdatePricesForCardsInList(listID);
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