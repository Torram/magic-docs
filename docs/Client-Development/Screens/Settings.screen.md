Component that creates a container for the buttons of settings screen.

```tsx
import UpdateDatabaseFromShopifyButton from '../components/ShopifyLogs/UpdateDatabaseFromShopifyButton';
import UpdateSetsButton from '../components/Sets/UpdateSetsButton';
import Container from 'react-bootstrap/Container';
import Row from 'react-bootstrap/Row';
import Col from 'react-bootstrap/Col';

export function SettingsScreen(): JSX.Element {
    return (
        <Container fluid>
            <Row>
                <Col xs={12}>
                    <UpdateDatabaseFromShopifyButton />
                </Col>
            </Row>
            <Row>
                <Col xs={12} className='mt-2'>
                    <UpdateSetsButton />
                </Col>
            </Row>
        </Container>
    );
}
```