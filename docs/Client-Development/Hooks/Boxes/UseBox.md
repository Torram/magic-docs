This custom hook gets a single box of the database, with the provided id.

```ts
import { useQuery, UseQueryResult } from 'react-query';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { basePath } from '../../config/basePath';
import { getToken } from '../../tokens/getToken';

async function getBox(id: string): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/boxes/${id}`, {
        method: 'GET',
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export function useBox(id: string): UseQueryResult<any, unknown> {
    return useQuery(['boxes', 'id', id], async () => {
        return await getBox(id);
    });
}
```