---
sidebar_position: 2
---

This function returns a _<Form\>_ element from the **react-bootstrap** module and contains the inputs for "email" and "password" for Login.

```tsx
function SignIn() {
    const { mutateAsync, error, isError, isLoading } = useSignIn();
    const history = useHistory();

    const handleSubmit = async (event) => {
        event.preventDefault();
        const data = new FormData(event.target);
        const email = data.get('email');
        const password = data.get('password');

        await mutateAsync({ email, password });
        history.push('/dashboard');
    };

    return (
        <Form onSubmit={handleSubmit} className='py-3'>
            <Form.Group controlId='email'>
                <Form.Label>Email</Form.Label>
                <Form.Control type='email' name='email' />
            </Form.Group>
            <Form.Group controlId='password'>
                <Form.Label>Contraseña</Form.Label>
                <Form.Control type='password' name='password' />
            </Form.Group>
            <ErrorIndicator isError={isError} error={error} />
            <LoadingButton className={'w-100'} type='submit' variant='success' disabled={isLoading} loading={isLoading}>
                Iniciar Sesión
            </LoadingButton>
        </Form>
    );
}

```