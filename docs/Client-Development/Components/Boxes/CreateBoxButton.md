---
sidebar_position: 3
---

This component is used to generate a button that creates a new box for a set.

## Functions

### CreateBoxButton

The function receives a setID of _string_ type and returns a JSX.Element.

```tsx
import LoadingButton from '../shared/LoadingButton';
import { useCreateBoxForSet } from '../../hooks/Boxes/useCreateBoxForSet';
import ErrorIndicator from '../shared/ErrorIndicator';

export default function CreateBoxButton({ setID }: { setID: string }): JSX.Element {
    const { mutate, isLoading, isError, error } = useCreateBoxForSet();

    const handleClick = (): void => {
        mutate(setID);
    };

    return (
        <div className={'w-100'}>
            <ErrorIndicator isError={isError} error={error} />
            <LoadingButton className={'w-100'} variant={'outline-secondary'} loading={isLoading} onClick={handleClick}>
                Crear caja
            </LoadingButton>
        </div>
    );
}
```