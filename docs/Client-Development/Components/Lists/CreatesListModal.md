---
sidebar_position: 2
---

Component that creates a modal with an input field to create a new list (binder).

## Functions

### CreateListModal

Main function that creates the modal with the input.


### handlleNameChange

Local function that handles input change with _name_ state.

### handleSave

Local function that creates a list (binder) with the provided name.

```tsx
import Modal from 'react-bootstrap/Modal';
import Button from 'react-bootstrap/Button';
import LoadingButton from '../shared/LoadingButton';
import useCreateList from '../../hooks/Lists/useCreateList';
import ErrorIndicator from '../shared/ErrorIndicator';
import React, { useState } from 'react';
import Form from 'react-bootstrap/Form';

export default function CreateListModal({ hideModal, show }: { hideModal: any; show: boolean }): JSX.Element {
    const { mutateAsync, isError, error, isLoading } = useCreateList();
    const [name, setName] = useState('');

    const handleNameChange = (event: React.ChangeEvent<HTMLInputElement>): void => setName(event.currentTarget.value);

    const handleSave = async (): Promise<void> => {
        await mutateAsync(name);
        hideModal();
    };

    return (
        <Modal show={show} onHide={hideModal}>
            <Modal.Header closeButton>
                <Modal.Title>Crear Carpeta</Modal.Title>
            </Modal.Header>
            <Modal.Body>
                <Form>
                    <Form.Group controlId='name'>
                        <Form.Label>Name</Form.Label>
                        <Form.Control onChange={handleNameChange} type='text' name='name' />
                    </Form.Group>
                </Form>
                <ErrorIndicator isError={isError} error={error} />
            </Modal.Body>
            <Modal.Footer>
                <Button variant='secondary' onClick={hideModal}>
                    Cerrar
                </Button>
                <LoadingButton loading={isLoading} variant='primary' onClick={handleSave}>
                    Crear
                </LoadingButton>
            </Modal.Footer>
        </Modal>
    );
}
```