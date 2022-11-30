---
sidebar_position: 3
---

This function returns a _<Form\>_ element from the **react-bootstrap** module, necessary to register a new user in the system.

```tsx
import { FormEvent } from 'react';
import Form from 'react-bootstrap/Form';
import LoadingButton from '../shared/LoadingButton';
import { useSignUp } from '../../hooks/Auth/useSignUp';
import ErrorIndicator from '../shared/ErrorIndicator';
import { useHistory } from 'react-router-dom';

function SignUp(): JSX.Element {
    const { mutateAsync, error, isError, isLoading } = useSignUp();
    const history = useHistory();

    const handleSubmit = async (event: FormEvent<HTMLFormElement>): Promise<void> => {
        event.preventDefault();
        const data = new FormData(event.currentTarget);
        const email = data.get('email') ?? '';
        const password = data.get('password') ?? '';
        const key = data.get('key') ?? '';
        const name = data.get('name') ?? '';
        const lastname = data.get('lastname') ?? '';

        await mutateAsync({ email, password, key, name, lastname });
        history.push('/dashboard');
    };

    return (
        <Form className='py-3' onSubmit={handleSubmit}>
            <Form.Group controlId='email'>
                <Form.Label>Email</Form.Label>
                <Form.Control type='email' name='email' />
            </Form.Group>
            <Form.Group controlId='name'>
                <Form.Label>Nombre</Form.Label>
                <Form.Control type='text' name='name' />
            </Form.Group>
            <Form.Group controlId='lastname'>
                <Form.Label>Apellido</Form.Label>
                <Form.Control type='text' name='lastname' />
            </Form.Group>
            <Form.Group controlId='password'>
                <Form.Label>Contraseña</Form.Label>
                <Form.Control type='password' name='password' />
            </Form.Group>
            <Form.Group controlId='key'>
                <Form.Label>Admin Key</Form.Label>
                <Form.Control type='key' name='key' />
            </Form.Group>
            <ErrorIndicator isError={isError} error={error} />
            <LoadingButton className='w-100' type='submit' variant='success' disabled={isLoading} loading={isLoading}>
                Crear Cuenta
            </LoadingButton>
        </Form>
    );
}

export default SignUp;
```