Custom hook that queries authentication data for a user.

```tsx
import { useMutation, UseMutationResult } from 'react-query';
import { getJSONHeaders } from '../../functions/sharedHeaders';
import { basePath } from '../../config/basePath';
import { saveTokenInLocalStorage } from '../../tokens/saveTokenInLocalStorage';
import { saveRefreshTokenInLocalStorage } from '../../tokens/saveRefreshTokenInLocalStorage';

async function signIn({ email, password }: { email: FormDataEntryValue; password: FormDataEntryValue }): Promise<any> {
    const response = await fetch(`${basePath}/auth/sign_in`, {
        method: 'POST',
        body: JSON.stringify({ password, email }),
        headers: getJSONHeaders()
    });

    const { token, refreshToken, user } = await response.json();

    if (token != null && refreshToken != null && response.status === 200) {
        saveRefreshTokenInLocalStorage(refreshToken);
        saveTokenInLocalStorage(token);
    } else {
        throw Error('No tokens provided');
    }

    return user;
}

export function useSignIn(): UseMutationResult<any, unknown, { email: FormDataEntryValue; password: FormDataEntryValue }, unknown> {
    return useMutation(signIn);
}
```