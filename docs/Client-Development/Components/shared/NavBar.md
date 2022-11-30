---
sidebar_position: 6
---

This component is the main navbar of the website. It shows a dynamic user name and every main link.

```tsx
import Navbar from 'react-bootstrap/Navbar';
import Nav from 'react-bootstrap/Nav';
import { Link } from 'react-router-dom';
import { UserType } from '../../types/UserType';

export default function NavBar({ user }: { user: UserType }): JSX.Element {
    return (
        <Navbar expand='lg' className='w-100'>
            <Navbar.Brand>{user.name}</Navbar.Brand>
            <Navbar.Toggle aria-controls='basic-navbar-nav' />
            <Navbar.Collapse id='basic-navbar-nav'>
                <Nav className='mr-auto'>
                    <Link className={'mb-sm-2 mb-lg-0 mr-lg-2'} data-as={Nav.Link} to={'/dashboard/boxes'}>
                        Cajas
                    </Link>
                    <Link className={'mb-sm-2 mb-lg-0 mr-lg-2'} data-as={Nav.Link} to={'/dashboard/lists'}>
                        Carpetas
                    </Link>
                    <Link className={'mb-sm-2 mb-lg-0 mr-lg-2'} data-as={Nav.Link} to={'/dashboard/shopify_logs'}>
                        Shopify Logs
                    </Link>
                    <Link className={'mb-sm-2 mb-lg-0 mr-lg-2'} data-as={Nav.Link} to={'/dashboard/inventory_logs'}>
                        Inventory Logs
                    </Link>
                    <Link className={'mb-sm-2 mb-lg-0'} data-as={Nav.Link} to={'/dashboard/settings'}>
                        Settings
                    </Link>
                </Nav>
            </Navbar.Collapse>
        </Navbar>
    );
}
```