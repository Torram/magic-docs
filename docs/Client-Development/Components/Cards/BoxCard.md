---
sidebar_position: 1
---

This component renders a card item, in the list of **[CardsBox](./CardsBox)**.

## Functions

### BoxCard

This function receives a card, of CardType, and an inventoryItem, of **[InventoryItemType](../../types)**; and returns a _row_ element.

```tsx
import Row from 'react-bootstrap/Row';
import Col from 'react-bootstrap/Col';
import { InventoryItem } from '../Inventory/InventoryItem';
import { CardType } from '../../types/CardType';
import { InventoryItemType } from '../../types/InventoryItemType';

export default function BoxCard({ card, inventoryItem }: { card: CardType; inventoryItem: InventoryItemType }): JSX.Element {
    const price = card.prices[card.prices.length - 1];

    return (
        <Row xs={12} md={4} lg={3} className={'mb-3 align-items-center'}>
            <Col>
                <h5 data-align={'center'} className={'fw-bolder'}>
                    {card.name}
                </h5>
            </Col>
            <Col className={'d-flex flex-column align-items-center'}>
                <span>SKU: {card.sku}</span>
            </Col>
            <Col className={'d-flex flex-column align-items-center'}>
                <span>${price?.value} USD</span>
            </Col>
            <Col>
                <InventoryItem cardID={card._id} inventoryItem={inventoryItem} />
            </Col>
        </Row>
    );
}
```