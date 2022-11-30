This custom hook gets all of the lists (binders) in the database.

```tsx
import { useQuery, UseQueryResult } from 'react-query';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { basePath } from '../../config/basePath';
import { getToken } from '../../tokens/getToken';

async function getLists(): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/lists/`, {
        method: 'GET',
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export function useLists(): UseQueryResult<any, unknown> {
    return useQuery('lists', getLists);
}
```