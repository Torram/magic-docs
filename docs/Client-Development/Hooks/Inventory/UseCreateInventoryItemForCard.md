This custom hook creates a new _InventoryItem_ for a card, in the provided set box or list (binder).

```tsx
import { useParams } from 'react-router-dom';
import { getToken } from '../../tokens/getToken';
import { basePath } from '../../config/basePath';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { useMutation, useQueryClient, UseMutationResult } from 'react-query';

async function createInventoryItemForCardAndBox(cardID: string, boxID: string, value: number): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/inventory/box/${boxID}`, {
        method: 'POST',
        body: JSON.stringify({
            cardID,
            value
        }),
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

async function createInventoryItemForCardAndList(cardID: string, listID: string, value: number): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/inventory/list/${listID}`, {
        method: 'POST',
        body: JSON.stringify({
            cardID,
            value
        }),
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export default function useCreateInventoryItemForCard(): UseMutationResult<any, unknown, { cardID: string; value: number }, unknown> {
    const { boxID, listID } = useParams<{ boxID: string; listID: string }>();
    const queryClient = useQueryClient();

    return useMutation(
        async ({ cardID, value }: { cardID: string; value: number }) => {
            if (boxID != null) {
                return await createInventoryItemForCardAndBox(cardID, boxID, value);
            } else if (listID != null) {
                return await createInventoryItemForCardAndList(cardID, listID, value);
            } else {
                throw Error('No box or list id');
            }
        },
        {
            onSuccess: async () => {
                return await queryClient.invalidateQueries('inventory');
            }
        }
    );
}
```