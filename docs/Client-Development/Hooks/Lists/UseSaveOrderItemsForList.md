This custom hook updates a list of _orderItem_ objects in a list (binder).

```tsx
import { getToken } from '../../tokens/getToken';
import { basePath } from '../../config/basePath';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { useMutation, useQueryClient, UseMutationResult } from 'react-query';
import { OrderItemType } from '../../types/OrderItemType';

async function saveOrderItemsForList({ orderItems, listID }: { orderItems: OrderItemType[]; listID: string }): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/lists/${listID}/order_items/`, {
        method: 'PUT',
        body: JSON.stringify({ orderItems }),
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export function useSaveOrderItemsForList(orderItems: OrderItemType[], listID: string): UseMutationResult<any, unknown, void, unknown> {
    const queryClient = useQueryClient();

    return useMutation(
        async () => {
            return await saveOrderItemsForList({ orderItems, listID });
        },
        {
            onSuccess: async () => {
                return await queryClient.invalidateQueries('list');
            }
        }
    );
}
```