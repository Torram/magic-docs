---
sidebar_position: 1
---

Function called for checking  unauthorized access to system and redirects user to login page.

This function calls a React component "AuthenticationCheck", imported from **@bma98/fractal-auth-screen** repository.

```tsx
import React from 'react';
import { Redirect } from 'react-router-dom';
import { AuthenticationCheck } from '@bma98/fractal-auth-screen';
import { getToken } from '../../tokens/getToken';
import LoadingScreen from '../../screens/Loading.screen';

export default function AuthenticationGuard({ children }) {
    return (
        <AuthenticationCheck
            key={'AuthenticationRedirect'}
            checkIfShouldAllowAccess={getToken}
            loadingComponent={<LoadingScreen />}
            redirectComponent={<Redirect to={'/authentication'} />}
        >
            {children}
        </AuthenticationCheck>
    );
}
```
