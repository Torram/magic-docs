---
sidebar_position: 2
---

This component is just used to generate link to a box of a set

## Functions

### BoxLink

This function receives a box as parameter, of the _BoxType_ type, and returns a link to the box.

```tsx
import { Link } from 'react-router-dom';
import Nav from 'react-bootstrap/Nav';
import { BoxType } from '../../types/BoxType';

export default function BoxLink({ box }: { box: BoxType }): JSX.Element {
    return (
        <Link className={'m-2'} data-as={Nav.Link} to={`/dashboard/boxes/${box._id}`}>
            {box.name}
        </Link>
    );
}
```