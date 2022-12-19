Custom hook that gets all of the cards in the provided set.

```tsx
import { useQuery, UseQueryResult } from 'react-query';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { basePath } from '../../config/basePath';
import { getToken } from '../../tokens/getToken';

async function getCardsForSet(setID: string): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/cards/set/${setID}`, {
        method: 'GET',
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export function useCardsForSet(setID: string): UseQueryResult<any, unknown> {
    return useQuery(['cards', 'set', setID], async () => {
        return await getCardsForSet(setID);
    });
}
```