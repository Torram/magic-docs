Custom hook that gets a specific list (binder).

```tsx
import { useQuery, UseQueryResult } from 'react-query';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { basePath } from '../../config/basePath';
import { getToken } from '../../tokens/getToken';

async function getList(listID: string): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/lists/${listID}`, {
        method: 'GET',
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export function useList(listID: string): UseQueryResult<any, unknown> {
    return useQuery(['lists', listID], async () => {
        return await getList(listID);
    });
}
```