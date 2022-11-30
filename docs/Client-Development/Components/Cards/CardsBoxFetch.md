---
sidebar_position: 4
---

This component creates a box of cards, of the provided setId.

## Functions

### CardsBoxFetch

This function receives a setId string, and uses the imported hook _useCardsforSet_ to get all cards in the provided set.

```tsx
import { useCardsForSet } from '../../hooks/Cards/useCardsForSet';
import Container from 'react-bootstrap/Container';
import Row from 'react-bootstrap/Row';
import Col from 'react-bootstrap/Col';
import CardsBox from './CardsBox';
import LoadingAndErrorCentered from '../shared/LoadingAndErrorCentered';

export default function CardsBoxFetch({ setID }: { setID: string }): JSX.Element {
    const { isLoading, isError, error, data } = useCardsForSet(setID);

    return (
        <Container>
            <Row>
                <Col xs={12}>
                    <LoadingAndErrorCentered isLoading={isLoading} isError={isError} error={error} />
                    {Boolean(data) && <CardsBox cards={data} />}
                </Col>
            </Row>
        </Container>
    );
}
```