Component that creates a container page for box management.

```tsx
import { useBox } from '../hooks/Boxes/useBox';
import { useParams } from 'react-router-dom';
import Container from 'react-bootstrap/Container';
import Row from 'react-bootstrap/Row';
import Col from 'react-bootstrap/Col';
import CardsBoxFetch from '../components/Cards/CardsBoxFetch';
import { BoxActions } from '../components/Boxes/BoxActions';
import LoadingAndErrorCentered from '../components/shared/LoadingAndErrorCentered';
import ContentCenterDiv from '../components/shared/ContentCenterDiv';

export default function BoxScreen(): JSX.Element {
    const { boxID } = useParams<{ boxID: string }>();
    const { isError, isLoading, error, data } = useBox(boxID);

    return (
        <Container>
            <Row>
                <Col xs={12}>
                    <LoadingAndErrorCentered isLoading={isLoading} isError={isError} error={error} />
                </Col>
                {Boolean(data) && (
                    <>
                        <Col xs={12}>
                            <ContentCenterDiv>
                                <h1 data-align={'center'}>{data.name}</h1>
                            </ContentCenterDiv>
                        </Col>
                        <BoxActions setID={data.set} />
                        <CardsBoxFetch setID={data.set} />
                    </>
                )}
            </Row>
        </Container>
    );
}
```