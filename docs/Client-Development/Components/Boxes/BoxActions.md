---
sidebar_position: 1
---

## Functions

### BoxActions

This functions takes _<Row\>_, _<Col\>_ and _<Container\>_ from **"react-bootstrap"** module.

This function shows buttons for box actions (as _createBoxButton_ or _UpdatePricesForCardsInSetButton_ ) of a box of a set.

```tsx
export function BoxActions({ setID }) {
    return (
        <Container className={'mb-2'}>
            <Row>
                <Col xs={12} md={4}>
                    <CreateBoxButton setID={setID} />
                </Col>
                <Col xs={12} md={4}>
                    <UpdatePricesForCardsInSetButton setID={setID} />
                </Col>
                <Col>
                    <UpdateShopifyValuesForSetButton setID={setID} />
                </Col>
            </Row>
        </Container>
    );
}
```