Custom hook that get the specified card of a box or a list (binder).

```tsx
import { useParams } from 'react-router-dom';
import { getToken } from '../../tokens/getToken';
import { basePath } from '../../config/basePath';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { useQuery } from 'react-query';

async function getInventoryItemForCardAndBox(cardID: string, boxID: string): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/inventory/box/${boxID}?cardID=${cardID}`, {
        method: 'GET',
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

async function getInventoryItemForCardAndList(cardID: string, listID: string): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/inventory/list/${listID}?cardID=${cardID}`, {
        method: 'GET',
        headers: getTokenAuthHeaders(token)
    });

    return response.json();
}

export default function useInventoryItemForCard(cardID: string) {
    const { boxID, listID } = useParams<{ boxID: string; listID: string }>();

    return useQuery(['inventory', cardID, boxID, listID], () => {
        if (boxID != null) {
            return getInventoryItemForCardAndBox(cardID, boxID);
        } else if (listID != null) {
            return getInventoryItemForCardAndList(cardID, listID);
        } else {
            throw Error('No box or list id');
        }
    });
}
```