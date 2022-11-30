This custom hook gets all of the cards in a list, of the provided listID.

```ts
import { useQuery, UseQueryResult } from 'react-query';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { basePath } from '../../config/basePath';
import { getToken } from '../../tokens/getToken';
import { CardType } from '../../types/CardType';

async function getCardsForList(listID: string): Promise<CardType[]> {
    const token = await getToken();
    const response = await fetch(`${basePath}/cards/list/${listID}`, {
        method: 'GET',
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export function useCardsForList(listID: string): UseQueryResult<CardType[]> {
    return useQuery(['cards', 'list', listID], async () => {
        return await getCardsForList(listID);
    });
}
```