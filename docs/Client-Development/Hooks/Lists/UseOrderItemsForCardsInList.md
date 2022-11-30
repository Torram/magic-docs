Custom hook that gets all of the _orderItems_ (cards) in a list (binder).

```tsx
import { getToken } from '../../tokens/getToken';
import { basePath } from '../../config/basePath';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { useQuery, UseQueryResult } from 'react-query';
import { ListType } from '../../types/ListType';

async function getOrderItemsForCardsInList(listID: string): Promise<ListType> {
    const token = await getToken();
    const response = await fetch(`${basePath}/lists/${listID}`, {
        method: 'GET',
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export default function useOrderItemsForCardsInList(listID: string): UseQueryResult<ListType> {
    return useQuery(['list', listID], async () => {
        return await getOrderItemsForCardsInList(listID);
    });
}
```