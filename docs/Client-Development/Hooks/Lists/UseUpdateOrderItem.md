This custom hook updates the value of an _orderItem_ in a list (binder).

```tsx
import { getToken } from '../../tokens/getToken';
import { basePath } from '../../config/basePath';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { useMutation, useQueryClient, UseMutationResult } from 'react-query';

async function updateObjectItem({ orderItemID, value, listID }: { orderItemID: string; value: number; listID: string }): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/lists/${listID}/order_item/${orderItemID}`, {
        method: 'PUT',
        body: JSON.stringify({ value }),
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export default function useUpdateObjectItem(): UseMutationResult<
    any,
    unknown,
    { orderItemID: string; value: number; listID: string },
    unknown
> {
    const queryClient = useQueryClient();

    return useMutation(updateObjectItem, {
        onSuccess: async () => {
            return await queryClient.invalidateQueries('list');
        }
    });
}
```