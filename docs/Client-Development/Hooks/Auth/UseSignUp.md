Custom hook that sends a form with all of the user's data for signup.

```tsx
import { useMutation, UseMutationResult } from 'react-query';
import { getJSONHeaders } from '../../functions/sharedHeaders';
import { basePath } from '../../config/basePath';
import { saveTokenInLocalStorage } from '../../tokens/saveTokenInLocalStorage';
import { saveRefreshTokenInLocalStorage } from '../../tokens/saveRefreshTokenInLocalStorage';

async function signUp({
    password,
    key,
    name,
    lastname,
    email
}: {
    password: FormDataEntryValue;
    key: FormDataEntryValue;
    name: FormDataEntryValue;
    lastname: FormDataEntryValue;
    email: FormDataEntryValue;
}): Promise<any> {
    const response = await fetch(`${basePath}/auth/sign_up`, {
        method: 'POST',
        body: JSON.stringify({ password, key, name, lastname, email }),
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

export function useSignUp(): UseMutationResult<
    any,
    unknown,
    {
        password: FormDataEntryValue;
        key: FormDataEntryValue;
        name: FormDataEntryValue;
        lastname: FormDataEntryValue;
        email: FormDataEntryValue;
    },
    unknown
> {
    return useMutation(signUp);
}
```