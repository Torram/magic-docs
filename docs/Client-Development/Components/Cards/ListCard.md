---
sidebar_position: 7
---

This component generates a card item for the **[CardsList](./CardsList)** component.

## Functions

### ListCard

This function receives a card parameter, of CardType; an inventoryItem, of InventoryItemType and a listId string. The functions returns a _Row_ element with the most important data of the card.

```tsx
import Col from 'react-bootstrap/Col';
import Row from 'react-bootstrap/Row';
import Image from 'react-bootstrap/Image';
import { InventoryItem } from '../Inventory/InventoryItem';
import { OrderItem } from '../Lists/OrderItem';
import useOrderItemsForCardsInList from '../../hooks/Lists/useOrderItemsForCardsInList';
import { CardType } from '../../types/CardType';
import { InventoryItemType } from '../../types/InventoryItemType';
import { OrderItemType } from '../../types/OrderItemType';

export default function ListCard({
    card,
    inventoryItem,
    listID
}: {
    card: CardType;
    inventoryItem: InventoryItemType;
    listID: string;
}): JSX.Element {
    const price = card.prices[card.prices.length - 1];
    const orderItemsData = useOrderItemsForCardsInList(listID);
    const orderItemForCard =
        orderItemsData != null ? orderItemsData.data?.orderItems?.find((orderItem: OrderItemType) => orderItem.card === card._id) : null;

    return (
        <Row className={'mr-3'}>
            <Col xs={20} className={'mb-3 mr-1 d-flex flex-column align-items-center'}>
                <h5 data-align={'center'} className={'fw-bolder'}>
                    {card.name}
                </h5>
                <span>SKU: {card.sku}</span>
                <InventoryItem cardID={card._id} inventoryItem={inventoryItem} />
                <span>${price?.value} USD</span>
                <Image style={{ width: 240, height: 330 }} className={'mt-2'} src={card.imageURL} rounded />
                <br />
                <OrderItem cardID={card._id} orderItem={orderItemForCard} listID={listID} />
            </Col>
        </Row>
    );
}
```