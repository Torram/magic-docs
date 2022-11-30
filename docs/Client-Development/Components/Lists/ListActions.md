---
sidebar_position: 3
---

Component that creates a container element with the possible actions for a list (binder), like add a card to the list, update prices for the cards in the list and update quantities from inventory to shopify of the cards in the list.

```tsx
import Col from 'react-bootstrap/Col';
import React, { useState } from 'react';
import Button from 'react-bootstrap/Button';
import AddCardToListModal from './AddCardToListModal';
import UpdatePriceForCardsInListButton from '../Cards/UpdatePriceForCardsInListButton';
import UpdateShopifyValuesForListButton from '../Cards/UpdateShopifyValuesForListButton';
import Row from 'react-bootstrap/Row';
import Container from 'react-bootstrap/Container';

export default function ListActions({ listID }: { listID: string }): JSX.Element {
    const [showModal, setShowModal] = useState(false);
    const toggleModal = (): void => setShowModal(!showModal);

    return (
        <Container fluid>
            <Row>
                <Col>
                    <Button variant={'outline-secondary'} className={'w-100'} onClick={toggleModal}>
                        Agregar tarjeta
                    </Button>
                    <AddCardToListModal listID={listID} show={showModal} hideModal={toggleModal} />
                </Col>
                <Col>
                    <UpdatePriceForCardsInListButton listID={listID} />
                </Col>
                <Col>
                    <UpdateShopifyValuesForListButton listID={listID} />
                </Col>
            </Row>
        </Container>
    );
}
```