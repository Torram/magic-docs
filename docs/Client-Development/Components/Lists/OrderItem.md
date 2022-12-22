Component that shows the actual position value of a card in a list (binder), and allows to change that value.

## Functions

### OrderItem

Main function of the component. This function sets the initial state of a card's position value, and the page that holds it. Creates a container with a _Form_ element with an input for a card's position value, an _ErrorIndicator_ element and a _LoadingButton_ for saving changes.

### checkIfNumberHasDecimalPoint

Local function that checks if a given number has a decimal point.

### saveNewValue

Local function that saves the new position value card in a list (binder).

### setNewValue

Local function that checks if the new position value for a card is valid before changing it.

```tsx
import ErrorIndicator from '../shared/ErrorIndicator';
import { useState } from 'react';
import Form from 'react-bootstrap/Form';
import useCreateOrderItem from '../../hooks/Lists/useCreateOrderItem';
import useUpdateObjectItem from '../../hooks/Lists/useUpdateOrderItem';
import LoadingButton from '../shared/LoadingButton';
import { orderItemsAtom } from '../../atoms/orderItemsAtom';
import { useSetRecoilState } from 'recoil';
import { OrderItemType } from '../../types/OrderItemType';

export function OrderItem({
    cardID,
    orderItem,
    listID
}: {
    cardID: string;
    orderItem: OrderItemType | null | undefined;
    listID: string;
}): JSX.Element {
    const createOrderItemMutation = useCreateOrderItem();
    const updateOrderItemMutation = useUpdateObjectItem();
    const [pageValue, setPageValue] = useState(orderItem != null ? orderItem.value : 0);
    const [pageValueChanged, setPageValueChanged] = useState(false);
    const setValue = useSetRecoilState(orderItemsAtom);

    const checkIfNumberHasDecimalPoint = (newPageValue: number): boolean => {
        const valueNumberString = newPageValue.toString();
        return valueNumberString.includes('.');
    };

    const saveNewValue = async (): Promise<void> => {
        if (orderItem != null) {
            await updateOrderItemMutation.mutateAsync({
                orderItemID: orderItem._id ?? '',
                value: pageValue ?? 0,
                listID
            });
        } else {
            await createOrderItemMutation.mutateAsync({
                cardId: cardID,
                valuePage: pageValue
            });
        }
        setValue([]);
        setPageValueChanged(false);
    };

    const setNewValue = (newPageValue: string): void => {
        if (newPageValue !== '') {
            const valueNumber = parseFloat(newPageValue);
            if (
                (!isNaN(valueNumber) && checkIfNumberHasDecimalPoint(valueNumber) && valueNumber !== pageValue) ||
                (!isNaN(valueNumber) && valueNumber === 0 && valueNumber !== pageValue)
            ) {
                setPageValueChanged(true);
                setPageValue(valueNumber);
                setValue((prev) => prev.concat({ value: valueNumber, card: cardID }));
            } else {
                setPageValueChanged(false);
            }
        } else {
            setPageValueChanged(false);
            setValue((prev) => prev.filter((_, index) => index !== prev.length - 1));
        }
    };

    return (
        <div className={'w-100'}>
            <Form className={'flex-shrink-10 mx-5'}>
                <Form.Group className='mb-0' controlId='formBasicEmail'>
                    <Form.Control
                        type='text'
                        placeholder={orderItem != null ? orderItem.value?.toString() : '0'}
                        onChange={(e) => setNewValue(e.target.value)}
                    />
                </Form.Group>
            </Form>
            <br />
            {pageValueChanged && (
                <>
                    <ErrorIndicator isError={createOrderItemMutation.isError} error={createOrderItemMutation.error} />
                    <LoadingButton
                        loading={createOrderItemMutation.isLoading}
                        variant={'outline-success'}
                        className={'w-100'}
                        onClick={saveNewValue}
                    >
                        Guardar
                    </LoadingButton>
                </>
            )}
        </div>
    );
}
```