Custom hook that gets all of the boxes for a set, with the provided setID.

```tsx
import { useQuery, UseQueryResult } from 'react-query';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { basePath } from '../../config/basePath';
import { getToken } from '../../tokens/getToken';

async function getBoxesForSet(setID: string): Promise<void> {
    const token = await getToken();
    const response = await fetch(`${basePath}/boxes/?setID=${setID}`, {
        method: 'GET',
        headers: getTokenAuthHeaders(token)
    });

    return response.json();
}

export function useBoxesForSet(setID: string): UseQueryResult<any, unknown> {
    return useQuery(['boxes', setID], () => {
        return getBoxesForSet(setID);
    });
}
```