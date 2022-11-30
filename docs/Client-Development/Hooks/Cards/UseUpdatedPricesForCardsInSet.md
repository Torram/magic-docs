Custom hook that calls a price update for all of the cards in a box o a set.

```tsx
import { getToken } from '../../tokens/getToken';
import { basePath } from '../../config/basePath';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { useMutation, useQueryClient, UseMutationResult } from 'react-query';

async function updatePricesForCardsInSet(setID: string): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/cards/set/${setID}`, {
        method: 'PUT',
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export function useUpdatePricesForCardsInSet(setID: string): UseMutationResult<any, unknown, void, unknown> {
    const queryClient = useQueryClient();

    return useMutation(
        async () => {
            return await updatePricesForCardsInSet(setID);
        },
        {
            onSuccess: async () => {
                return await queryClient.invalidateQueries(['cards', 'set', setID]);
            }
        }
    );
}
```