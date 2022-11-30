Custom hook that gets a token for an authenticated user.

```ts
import { useQuery, UseQueryResult } from 'react-query';
import { getToken } from '../../tokens/getToken';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { basePath } from '../../config/basePath';

async function getUser(): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/auth/user`, {
        method: 'GET',
        headers: getTokenAuthHeaders(token)
    });
    return await response.json();
}

export function useUser(): UseQueryResult<any, unknown> {
    return useQuery('user', getUser);
}
```