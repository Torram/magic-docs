---
sidebar_position: 4
---

Component that creates a _Link_ element to a cards list (binder)

```tsx
import { Link } from 'react-router-dom';
import Nav from 'react-bootstrap/Nav';
import { ListType } from '../../types/ListType';

export default function ListLink({ list }: { list: ListType }): JSX.Element {
    return (
        <Link className={'m-2'} data-as={Nav.Link} to={`/dashboard/lists/${list._id}`}>
            Revisar carpeta {list.name}
        </Link>
    );
}
```