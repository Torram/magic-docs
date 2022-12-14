---
sidebar_position: 2
---

This component is a text input with autocomplete function.

## Functions

### CardsAutoComplete

This function receives an _onChange_ property as parameter and returns the cards selector component.

```tsx
import Select from 'react-select';
import { useAllCards } from '../../hooks/Cards/useAllCards';
import ErrorIndicator from '../shared/ErrorIndicator';
import useAutoCompleteCards from '../../hooks/Cards/useAutoCompleteCards';

export default function CardsAutoComplete({ onChange }: { onChange: any }): JSX.Element {
    const { isError, error, isLoading, data } = useAllCards();
    const cards = useAutoCompleteCards(data);

    return (
        <>
            <div className={'w-100 d-flex flex-column align-items-center'}>
                <ErrorIndicator isError={isError} error={error} />
            </div>
            <Select isLoading={isLoading} isMulti closeMenuOnSelect={false} options={cards} onChange={onChange} />
        </>
    );
}
```