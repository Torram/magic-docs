This component creates a container with a **_[ListActions](./ListActions)_** element and a **_[ListLink](./ListLink)_** element for a list (binder).

```tsx
import Container from 'react-bootstrap/Container';
import Row from 'react-bootstrap/Row';
import Col from 'react-bootstrap/Col';
import ListLink from './ListLink';
import ListActions from './ListActions';
import ContentCenterDiv from '../shared/ContentCenterDiv';
import { ListType } from '../../types/ListType';

export default function ListRow({ list }: { list: ListType }): JSX.Element {
    return (
        <Container>
            <Row>
                <Col xs={12}>
                    <ContentCenterDiv>
                        <h3 data-align={'center'} className={'mb-4'}>
                            {list.name}
                        </h3>
                        <ListActions listID={list._id} />
                        <ListLink list={list} />
                    </ContentCenterDiv>
                </Col>
            </Row>
        </Container>
    );
}
```
