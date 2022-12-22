---
sidebar_position: 1
---

This component creates a modal that shows an input that searches for a specific card.

## Functions

### AddCardToListModal

Main function that creates a modal, with an input.

### handleSelectionChange

Local function that returns the value of the selected card.

### handleSave

Local function that creates an object with the _cardID_ and the _listID_ where the card is added.

```tsx
import { useState } from 'react';
import Modal from 'react-bootstrap/Modal';
import ErrorIndicator from '../shared/ErrorIndicator';
import Button from 'react-bootstrap/Button';
import LoadingButton from '../shared/LoadingButton';
import CardsAutoComplete from '../Cards/CardsAutoComplete';
import useUpdateList from '../../hooks/Lists/useUpdateList';
import { CardOptionType } from '../../types/CardOptionType';

export default function AddCardToListModal({
    show,
    hideModal,
    listID
}: {
    show: boolean;
    hideModal: Function;
    listID: string;
}): JSX.Element {
    const { mutateAsync, isError, error, isLoading } = useUpdateList();
    const [cardIDs, setCardIDs] = useState<string[]>([]);

    const handleSelectionChange = (cards: CardOptionType[]): void => {
        setCardIDs(
            cards.map((card) => {
                return card.value;
            })
        );
    };

    const handleSave = async (): Promise<void> => {
        await mutateAsync({ cards: cardIDs, listID });
        hideModal();
    };

    return (
        <Modal show={show} onHide={hideModal}>
            <Modal.Header closeButton>
                <Modal.Title>Agregar tarjeta</Modal.Title>
            </Modal.Header>
            <Modal.Body>
                <CardsAutoComplete onChange={handleSelectionChange} />
                <ErrorIndicator isError={isError} error={error} />
            </Modal.Body>
            <Modal.Footer>
                <Button variant='secondary' onClick={() => hideModal()}>
                    Cerrar
                </Button>
                <LoadingButton loading={isLoading} variant='primary' onClick={handleSave}>
                    Agregar
                </LoadingButton>
            </Modal.Footer>
        </Modal>
    );
}
```