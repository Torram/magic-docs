Component that creates a _LoadingButton_ that saves all the changes in the order of cards in a list (binder).

```tsx
import { useSaveOrderItemsForList } from '../../hooks/Lists/useSaveOrderItemsForList';
import ErrorIndicator from '../shared/ErrorIndicator';
import LoadingButton from '../shared/LoadingButton';
import { orderItemsAtom } from '../../atoms/orderItemsAtom';
import { useSetRecoilState } from 'recoil';
import { OrderItemType } from '../../types/OrderItemType';

export default function SaveOrderItemsButton({ orderItems, listID }: { orderItems: OrderItemType[]; listID: string }): JSX.Element {
    const { isError, error, isLoading, mutate } = useSaveOrderItemsForList(orderItems, listID);
    const setValue = useSetRecoilState(orderItemsAtom);

    return (
        <div className={'w-100'}>
            <br />
            <ErrorIndicator isError={isError} error={error} />
            <LoadingButton
                className={'w-100'}
                variant={'success'}
                loading={isLoading}
                onClick={() => {
                    mutate();
                    setValue([]);
                }}
            >
                Guardar todos
            </LoadingButton>
        </div>
    );
}
```