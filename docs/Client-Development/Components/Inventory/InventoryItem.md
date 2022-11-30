---
sidebar_position: 1
---

This component adds the capacity of edit values of card items.


## Functions

### InventoryItem

Main function of the component. Includes functions for increase and decrease values of cards or manually set the value.

```tsx
import ErrorIndicator from '../shared/ErrorIndicator';
import React, { useState } from 'react';
import Button from 'react-bootstrap/Button';
import Form from 'react-bootstrap/Form';
import useCreateInventoryItemForCard from '../../hooks/Inventory/useCreateInventoryItemForCard';
import useUpdateInventoryItem from '../../hooks/Inventory/useUpdateInventoryItem';
import LoadingButton from '../shared/LoadingButton';
import { InventoryItemType } from '../../types/InventoryItemType';

export function InventoryItem({ cardID, inventoryItem }: { cardID: string; inventoryItem: InventoryItemType }): JSX.Element {
    const createInventoryItemMutation = useCreateInventoryItemForCard();
    const updateInventoryItemMutation = useUpdateInventoryItem();
    const [value, setValue] = useState(inventoryItem != null ? inventoryItem.quantity : 0);
    const [valueChanged, setValueChanged] = useState(false);

    const increaseValue = (): void => {
        setValue((current: number) => current + 1);
        setValueChanged(true);
    };

    const decreaseValue = (): void => {
        setValue((current: number) => current - 1);
        setValueChanged(true);
    };

    const saveNewValue = async (): Promise<void> => {
        if (inventoryItem != null) {
            await updateInventoryItemMutation.mutateAsync({
                value,
                inventoryItemID: inventoryItem._id
            });
        } else {
            await createInventoryItemMutation.mutateAsync({
                value,
                cardID
            });
        }
        setValueChanged(false);
    };

    const setNewValue = (e: React.ChangeEvent<HTMLFormElement>): void => {
        const value = e.currentTarget.value;
        if (value !== '') {
            const valueNumber = parseInt(value);
            if (!isNaN(valueNumber)) {
                setValue(valueNumber);
                setValueChanged(true);
            } else {
                setValue(inventoryItem != null ? inventoryItem.quantity : 0);
                setValueChanged(false);
            }
        } else {
            setValue(inventoryItem != null ? inventoryItem.quantity : 0);
            setValueChanged(false);
        }
    };

    return (
        <div className={'w-100'}>
            <div className={'d-flex w-100 justify-content-between align-items-center my-3'}>
                <Button onClick={decreaseValue} className={'flex-grow-1'}>
                    -
                </Button>
                <Form onSubmit={setNewValue} className={'flex-shrink-10 mx-4'}>
                    <Form.Group className='mb-0' controlId='formBasicEmail'>
                        <Form.Control type='text' placeholder={value != null ? value.toString() : 'ERROR'} onChange={() => setNewValue} />
                    </Form.Group>
                </Form>
                <Button onClick={increaseValue} className={'flex-grow-1'}>
                    +
                </Button>
            </div>
            {valueChanged && (
                <>
                    <ErrorIndicator isError={updateInventoryItemMutation.isError} error={updateInventoryItemMutation.error} />
                    <ErrorIndicator isError={createInventoryItemMutation.isError} error={createInventoryItemMutation.error} />
                    <LoadingButton
                        loading={updateInventoryItemMutation.isLoading || createInventoryItemMutation.isLoading}
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
