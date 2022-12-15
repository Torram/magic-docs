This component creates a _container_ element with all of the sets in the database. 

```tsx
import { useSets } from '../hooks/Sets/useSets';
import Container from 'react-bootstrap/Container';
import Row from 'react-bootstrap/Row';
import Col from 'react-bootstrap/Col';
import LoadingAndErrorCentered from '../components/shared/LoadingAndErrorCentered';
import ContentCenterDiv from '../components/shared/ContentCenterDiv';
import SetsList from '../components/Sets/SetsList';

export default function SetsScreen(): JSX.Element {
    const { isLoading, data, isError, error } = useSets();

    return (
        <Container fluid>
            <Row>
                <Col xs={12}>
                    <ContentCenterDiv>
                        <h1 data-align={'center'}>Sets</h1>
                    </ContentCenterDiv>
                </Col>
                <Col xs={12}>
                    <LoadingAndErrorCentered isLoading={isLoading} isError={isError} error={error} />
                </Col>
            </Row>
            {Boolean(data) && <SetsList sets={data} />}
        </Container>
    );
}
```