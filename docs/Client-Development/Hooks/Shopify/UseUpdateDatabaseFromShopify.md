Custom hook used for manually update values from Shopify e-commerce to database.

```tsx
import { useMutation, useQueryClient, UseMutationResult } from 'react-query';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { basePath } from '../../config/basePath';
import { getToken } from '../../tokens/getToken';

async function updateDatabaseFromShopify(): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/shopify/sync_database_with_shopify`, {
        method: 'POST',
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export function useUpdateDatabaseFromShopify(): UseMutationResult<any, unknown, void, unknown> {
    const queryClient = useQueryClient();

    return useMutation(updateDatabaseFromShopify, {
        onSuccess: async () => {
            return await queryClient.invalidateQueries();
        }
    });
}
```