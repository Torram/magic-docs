Custom hook that creates a _OrderItem_ for a list (binder).

```tsx
import { getToken } from '../../tokens/getToken';
import { basePath } from '../../config/basePath';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { useMutation, useQueryClient, UseMutationResult } from 'react-query';

async function createOrderItem({ cardId, listID, valuePage }: { cardId: string; listID?: string; valuePage?: number }): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/lists/${listID ?? ''}/order_item/`, {
        method: 'POST',
        body: JSON.stringify({ cardId, valuePage }),
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export default function useCreateOrderItem(): UseMutationResult<
    any,
    unknown,
    { cardId: string; listID?: string | undefined; valuePage?: number | undefined },
    unknown
> {
    const queryClient = useQueryClient();

    return useMutation(createOrderItem, {
        onSuccess: async () => {
            return await queryClient.invalidateQueries('list');
        }
    });
}
```