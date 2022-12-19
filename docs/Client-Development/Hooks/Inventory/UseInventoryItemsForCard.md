This custom hook gets all of the _inventoryItems_ in a list (binder) or a set box.

```tsx
import { getToken } from '../../tokens/getToken';
import { basePath } from '../../config/basePath';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { useParams } from 'react-router-dom';
import { useQuery, UseQueryResult } from 'react-query';
import { CardType } from '../../types/CardType';

async function getInventoryItemsForCardsInBox(boxID: string): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/inventory/box/${boxID}`, {
        method: 'GET',
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

async function getInventoryItemsForCardsInList(listID: string): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/inventory/list/${listID}`, {
        method: 'GET',
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export default function useInventoryItemsForCards(cards: CardType[]): UseQueryResult<any, unknown> {
    const { boxID, listID } = useParams<{ boxID: string; listID: string }>();

    return useQuery(['inventory', ...cards], async () => {
        if (boxID != null) {
            return await getInventoryItemsForCardsInBox(boxID);
        } else if (listID != null) {
            return await getInventoryItemsForCardsInList(listID);
        } else {
            throw Error('No box or list id');
        }
    });
}
```
