Custom hook that gets all of the logs of movements in Shopify e-commerce inventory.

```tsx
import { useQuery, UseQueryResult } from 'react-query';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { basePath } from '../../config/basePath';
import { getToken } from '../../tokens/getToken';

async function getShopifyLogs(): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/shopify/`, {
        method: 'GET',
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export function useShopifyLogs(): UseQueryResult<any, unknown> {
    return useQuery(['shopify'], getShopifyLogs);
}
```