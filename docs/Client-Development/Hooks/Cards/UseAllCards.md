Custom hook that gets all of the cards in the database.

:::danger HEAVY LOAD
:::

```tsx
import { useQuery, UseQueryResult } from 'react-query';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { basePath } from '../../config/basePath';
import { getToken } from '../../tokens/getToken';

async function getAllCards(): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/cards/`, {
        method: 'GET',
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export function useAllCards(): UseQueryResult<any, unknown> {
    return useQuery(['cards', 'all'], async () => {
        return await getAllCards();
    });
}
```