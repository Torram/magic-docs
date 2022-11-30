---
sidebar_position: 5
---

This component creates a list of cards, of the provided list (binder), ordered by its physical position in the binder.

## Functions

### CardsList

The function receives an array of cards of the _CardType_ and a listId string, and returns a ordered list of card items.

```tsx
import ListCard from './ListCard';
import ContentCenterDiv from '../shared/ContentCenterDiv';
import Row from 'react-bootstrap/Row';
import Col from 'react-bootstrap/Col';
import useInventoryItemsForCards from '../../hooks/Inventory/useInventoryItemsForCards';
import LoadingAndErrorCentered from '../shared/LoadingAndErrorCentered';
import { CardType } from '../../types/CardType';

export default function CardsList({ cards, listID }: { cards: CardType[][]; listID: string }): JSX.Element {
    const flattened = cards.flatMap((array) => array);
    const nuArray = flattened.filter((card) => card?._id);
    const { isLoading, isError, error, data } = useInventoryItemsForCards(nuArray);
    let indexOfCard = -1;

    return (
        <>
            <LoadingAndErrorCentered isLoading={isLoading} isError={isError} error={error} />
            <Row>
                <Col>
                    {Boolean(data) &&
                        cards.map((row, index) => {
                            return (
                                <ContentCenterDiv key={null}>
                                    <h2 style={{ textAlign: 'center' }}>Pagina {index + 1 < cards.length ? index + 1 : 'desacomodadas'}</h2>
                                    <Row className='justify-content-md-center'>
                                        {row.map((card, _) => {
                                            indexOfCard += 1;
                                            return (
                                                card != null && (
                                                    <ListCard
                                                        key={card._id}
                                                        card={card}
                                                        inventoryItem={data[indexOfCard]}
                                                        listID={listID}
                                                    />
                                                )
                                            );
                                        })}
                                    </Row>
                                </ContentCenterDiv>
                            );
                        })}
                </Col>
            </Row>
        </>
    );
}
```