Component that creates a container with a **_[BoxActions](../Boxes/BoxActions)_** element and a list of boxes for the provided _set_.

```tsx
import Row from 'react-bootstrap/Row';
import Col from 'react-bootstrap/Col';
import React from 'react';
import BoxLink from '../Boxes/BoxLink';
import { BoxActions } from '../Boxes/BoxActions';
import { SetType } from '../../types/SetType';
import { BoxType } from '../../types/BoxType';

export default function SetRow({ set, boxes }: { set: SetType; boxes: BoxType[] }): JSX.Element {
    return (
        <Row className={'mb-5'}>
            <Col xs={12}>
                <div className={'d-flex flex-column align-items-center'}>
                    <h3 data-align={'center'} className={'mb-4'}>
                        {set.name}
                    </h3>
                    <BoxActions setID={set._id} />
                    <div className={'d-flex'}>
                        {boxes.map((box) => {
                            return <BoxLink key={box._id.toString()} box={box} />;
                        })}
                    </div>
                </div>
            </Col>
        </Row>
    );
}
```