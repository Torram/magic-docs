Custom hook that gets all of the sets in database.

```tsx
import { useQuery, UseQueryResult } from 'react-query';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { basePath } from '../../config/basePath';
import { getToken } from '../../tokens/getToken';

async function getSets(): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/sets/`, {
        method: 'GET',
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export function useSets(): UseQueryResult<any, unknown> {
    return useQuery('sets', getSets);
}
```