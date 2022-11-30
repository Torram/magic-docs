---
sidebar_position: 6
---

This component creates a list of card items, of the physical list (binder), ordered by its physical order.

## Functions

### CardsListFetch

The function receives a listId string, and calls the hook _useCardsForList_ to get all cards assigned to that list, then, calls another hook, _useOrderItemsForCardsInList_ to order all the items. Finally, calls another hook, _usePaginateCardsInList_, for pagination.

```tsx
import Container from 'react-bootstrap/Container';
import Row from 'react-bootstrap/Row';
import Col from 'react-bootstrap/Col';
import CardsList from './CardsList';
import { useCardsForList } from '../../hooks/Cards/useCardsForList';
import useOrderItemsForCardsInList from '../../hooks/Lists/useOrderItemsForCardsInList';
import usePaginateCardsInList from '../../hooks/Cards/usePaginateCardsInList';
import LoadingAndErrorCentered from '../shared/LoadingAndErrorCentered';

export default function CardsListFetch({ listID }: { listID: string }): JSX.Element {
    const { isLoading, isError, error, data } = useCardsForList(listID);
    const { data: orderItems } = useOrderItemsForCardsInList(listID);
    const pagesArray = usePaginateCardsInList(orderItems?.orderItems ?? [], data ?? []) ?? [];

    return (
        <Container>
            <Row>
                <Col xs={12}>
                    <br />
                    <LoadingAndErrorCentered isLoading={isLoading} isError={isError} error={error} />
                    {data != null && <CardsList cards={pagesArray} listID={listID} />}
                </Col>
            </Row>
        </Container>
    );
}
```