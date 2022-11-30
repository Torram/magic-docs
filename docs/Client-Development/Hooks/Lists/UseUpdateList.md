Custom hook that gets the updated list (binder) after we add a card.

```tsx
import { getToken } from '../../tokens/getToken';
import { basePath } from '../../config/basePath';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { useMutation, useQueryClient, UseMutationResult } from 'react-query';

async function updateList({ cards, listID }: { cards: string[]; listID: string }): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/lists/${listID}`, {
        method: 'PUT',
        body: JSON.stringify({ cards }),
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export default function useUpdateList(): UseMutationResult<any, unknown, { cards: string[]; listID: string }, unknown> {
    const queryClient = useQueryClient();

    return useMutation(
        async ({ cards, listID }: { cards: string[]; listID: string }) => {
            return await updateList({ cards, listID });
        },
        {
            onSuccess: async () => {
                return await queryClient.invalidateQueries('lists');
            }
        }
    );
}
```