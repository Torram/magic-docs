Component that creates a container with all of the lists (binders) and their actions.

```tsx
import { useState } from 'react';
import Container from 'react-bootstrap/Container';
import { useLists } from '../hooks/Lists/useLists';
import Row from 'react-bootstrap/Row';
import ListRow from '../components/Lists/ListRow';
import Col from 'react-bootstrap/Col';
import Button from 'react-bootstrap/Button';
import CreateListModal from '../components/Lists/CreateListModal';
import ContentCenterDiv from '../components/shared/ContentCenterDiv';
import LoadingAndErrorCentered from '../components/shared/LoadingAndErrorCentered';
import { ListType } from '../types/ListType';

export default function ListsScreen(): JSX.Element {
    const { isLoading, data, isError, error } = useLists();
    const [showModal, setShowModal] = useState(false);
    const toggleShowModal = (): void => setShowModal(!showModal);

    return (
        <Container fluid>
            <Row>
                <Col xs={12}>
                    <ContentCenterDiv>
                        <h1 data-align={'center'}>Carpetas</h1>
                    </ContentCenterDiv>
                </Col>
                <Col xs={12}>
                    <ContentCenterDiv>
                        <Button onClick={toggleShowModal} variant={'outline-secondary'} style={{ width: 1100 }}>
                            Crear carpeta
                        </Button>
                        <CreateListModal show={showModal} hideModal={toggleShowModal} />
                    </ContentCenterDiv>
                </Col>
                <Col xs={12}>
                    <LoadingAndErrorCentered isLoading={isLoading} isError={isError} error={error} />
                </Col>
            </Row>
            <Row>
                <Col>
                    {Boolean(data) &&
                        data.map((list: ListType) => {
                            return <ListRow key={list._id.toString()} list={list} />;
                        })}
                </Col>
            </Row>
        </Container>
    );
}
```