---
sidebar_position: 3
---

This component renders a list of **[BoxCard](./BoxCard)** items.

## Functions

### CardsBox

This function receives a list of cards of _CardType_ and returns a JSX.Element that is a list of the cards.

```tsx
import BoxCard from './BoxCard';
import Row from 'react-bootstrap/Row';
import Col from 'react-bootstrap/Col';
import useInventoryItemsForCards from '../../hooks/Inventory/useInventoryItemsForCards';
import LoadingAndErrorCentered from '../shared/LoadingAndErrorCentered';
import { CardType } from '../../types/CardType';

export default function CardsBox({ cards }: { cards: CardType[] }): JSX.Element {
    const { isLoading, isError, error, data } = useInventoryItemsForCards(cards.filter((card) => card?._id));

    return (
        <>
            <LoadingAndErrorCentered isLoading={isLoading} isError={isError} error={error} />
            <Row>
                <Col className='justify-content-md-center'>
                    {Boolean(data) &&
                        cards.map((card, index) => {
                            return card != null && <BoxCard key={card._id.toString()} card={card} inventoryItem={data[index]} />;
                        })}
                </Col>
            </Row>
        </>
    );
}
```