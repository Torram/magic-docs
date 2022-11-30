Component that creates a container page for user authentication.

```tsx
import { useState } from 'react';
import Container from 'react-bootstrap/Container';
import Row from 'react-bootstrap/Row';
import Col from 'react-bootstrap/Col';
import SignIn from '../components/Auth/SignIn';
import SignUp from '../components/Auth/SignUp';
import Button from 'react-bootstrap/Button';

export default function AuthenticationScreen(): JSX.Element {
    const [showSignIn, setShowSignIn] = useState(true);

    const toggle = (): void => {
        setShowSignIn(!showSignIn);
    };

    return (
        <Container>
            <Row>
                <Col xs={1} sm={2} md={3} />
                <Col xs={10} sm={8} md={6}>
                    <div className='d-flex flex-column justify-content-center bg-white px-3 shadow-sm rounded mt-3 w-100'>
                        {showSignIn ? <SignIn /> : <SignUp />}
                        <Button className='mb-3' variant='primary' onClick={toggle}>
                            {showSignIn ? 'Crear Cuenta' : 'Iniciar Sesión'}
                        </Button>
                    </div>
                </Col>
                <Col xs={1} sm={2} md={3} />
            </Row>
        </Container>
    );
}
```