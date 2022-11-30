Component that creates the page for changes in Shopify e-commerce.

```tsx
import Container from 'react-bootstrap/Container';
import Row from 'react-bootstrap/Row';
import Col from 'react-bootstrap/Col';
import { useShopifyLogs } from '../hooks/Shopify/useShopifyLogs';
import { ShopifyLog } from '../components/ShopifyLogs/ShopifyLog';
import LoadingAndErrorCentered from '../components/shared/LoadingAndErrorCentered';
import { ShopifyLogType } from '../types/ShopifyLogType';

export default function ShopifyLogsScreen(): JSX.Element {
    const { isLoading, isError, error, data } = useShopifyLogs();

    return (
        <Container>
            <Row>
                <Col xs={12}>
                    <LoadingAndErrorCentered isLoading={isLoading} isError={isError} error={error} />
                </Col>
                <Col xs={12}>
                    {Boolean(data) &&
                        data.map((log: ShopifyLogType) => {
                            return <ShopifyLog key={log._id.toString()} log={log} />;
                        })}
                </Col>
            </Row>
        </Container>
    );
}
```
