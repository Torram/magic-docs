This custom hook is used for getting all of the boxes of all sets.

```tsx
import { useQuery, UseQueryResult } from 'react-query';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { basePath } from '../../config/basePath';
import { getToken } from '../../tokens/getToken';

async function getAllBoxes(): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/boxes/`, {
        method: 'GET',
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export function useAllBoxes(): UseQueryResult<any, unknown> {
    return useQuery('boxes', async () => {
        return await getAllBoxes();
    });
}
```