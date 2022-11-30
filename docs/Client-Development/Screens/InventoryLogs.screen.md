This component creates a container page to visualize logs of inventory changes.

```tsx
import { useInventoryLogs } from '../hooks/InventoryLogs/useInventoryLogs';
import Container from 'react-bootstrap/Container';
import Row from 'react-bootstrap/Row';
import Col from 'react-bootstrap/Col';
import { InventoryLog } from '../components/InventoryLogs/InventoryLog';
import LoadingAndErrorCentered from '../components/shared/LoadingAndErrorCentered';
import { InventoryLogType } from '../types/InventoryLogType';

export default function InventoryLogsScreen(): JSX.Element {
    const { isLoading, isError, error, data } = useInventoryLogs('1998-08-26', '2024-08-26');

    return (
        <Container>
            <Row>
                <Col xs={12}>
                    <LoadingAndErrorCentered isError={isError} isLoading={isLoading} error={error} />
                </Col>
                <Col xs={12}>
                    {Boolean(data) &&
                        data.map((log: InventoryLogType) => {
                            return <InventoryLog key={log._id.toString()} log={log} />;
                        })}
                </Col>
            </Row>
        </Container>
    );
}
```