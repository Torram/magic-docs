Custom hook that updates the value of an _inventoryItem_.

```tsx
import { getToken } from '../../tokens/getToken';
import { basePath } from '../../config/basePath';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { useMutation, useQueryClient, UseMutationResult } from 'react-query';

async function updateInventoryItem({ inventoryItemID, value }: { inventoryItemID: string; value: number }): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/inventory/${inventoryItemID}`, {
        method: 'PUT',
        body: JSON.stringify({ value }),
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export default function useUpdateInventoryItem(): UseMutationResult<any, unknown, { inventoryItemID: string; value: number }, unknown> {
    const queryClient = useQueryClient();

    return useMutation(updateInventoryItem, {
        onSuccess: async () => {
            return await queryClient.invalidateQueries('inventory');
        }
    });
}
```