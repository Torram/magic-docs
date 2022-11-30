Component that creates a list of **_[SetRow](./SetRow)_** elements.

```tsx
import { useAllBoxes } from '../../hooks/Boxes/useAllBoxes';
import SetRow from './SetRow';
import React from 'react';
import LoadingAndErrorCentered from '../shared/LoadingAndErrorCentered';
import { SetType } from '../../types/SetType';
import { BoxType } from '../../types/BoxType';

export default function SetsList({ sets }: { sets: SetType[] }): JSX.Element {
    const { isLoading, data, isError, error } = useAllBoxes();

    return (
        <>
            <LoadingAndErrorCentered isLoading={isLoading} isError={isError} error={error} />
            {data != null &&
                sets.map((set) => {
                    const boxesForSet = data.filter((box: BoxType) => box.set === set._id);
                    return <SetRow key={set._id.toString()} set={set} boxes={boxesForSet} />;
                })}
        </>
    );
}
```