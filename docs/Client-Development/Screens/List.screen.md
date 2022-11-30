Component that creates a container page for list management.

```tsx
import { useParams } from 'react-router-dom';
import Container from 'react-bootstrap/Container';
import Col from 'react-bootstrap/Col';
import Row from 'react-bootstrap/Row';
import { useList } from '../hooks/Lists/useList';
import CardsListFetch from '../components/Cards/CardsListFetch';
import ListActions from '../components/Lists/ListActions';
import SaveOrderItemsButton from '../components/Lists/SaveOrderItemsButton';
import LoadingAndErrorCentered from '../components/shared/LoadingAndErrorCentered';
import ContentCenterDiv from '../components/shared/ContentCenterDiv';
import { useRecoilValue } from 'recoil';
import { orderItemsAtom } from '../atoms/orderItemsAtom';

export default function ListScreen(): JSX.Element {
    const { listID } = useParams<{ listID: string }>();
    const { isError, isLoading, error, data } = useList(listID);
    const orderItems = useRecoilValue(orderItemsAtom);

    return (
        <Container>
            <Row>
                <Col xs={12}>
                    <LoadingAndErrorCentered isLoading={isLoading} isError={isError} error={error} />
                </Col>
            </Row>
            {Boolean(data) && (
                <>
                    <Row>
                        <Col xs={12}>
                            <ContentCenterDiv>
                                <h1 data-align={'center'}>{data.name}</h1>
                            </ContentCenterDiv>
                        </Col>
                    </Row>
                    <ListActions listID={data._id} />
                    {orderItems.length > 0 && (
                        <ContentCenterDiv className='sticky-top'>
                            <SaveOrderItemsButton orderItems={orderItems} listID={listID} />
                        </ContentCenterDiv>
                    )}
                    <CardsListFetch listID={data._id} />
                </>
            )}
        </Container>
    );
}
```