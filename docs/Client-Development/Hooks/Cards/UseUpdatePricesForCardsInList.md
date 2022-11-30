Custom hook that calls a price update for all of the cards in a list (binder).

```tsx
import { getToken } from '../../tokens/getToken';
import { basePath } from '../../config/basePath';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { useMutation, useQueryClient, UseMutationResult } from 'react-query';

async function updatePricesForCardsInList(listID: string): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/cards/list/${listID}`, {
        method: 'PUT',
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export function useUpdatePricesForCardsInList(listID: string): UseMutationResult<any, unknown, void, unknown> {
    const queryClient = useQueryClient();

    return useMutation(
        async () => {
            return await updatePricesForCardsInList(listID);
        },
        {
            onSuccess: async () => {
                return await queryClient.invalidateQueries(['cards', 'list', listID]);
            }
        }
    );
}
```