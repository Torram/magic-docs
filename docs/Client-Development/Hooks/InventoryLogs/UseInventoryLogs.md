Custom hook used for get all of the _inventoryLogs_ between specified dates.

```tsx
import { useQuery, UseQueryResult } from 'react-query';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { basePath } from '../../config/basePath';
import { getToken } from '../../tokens/getToken';

async function getInventoryLogs(startDate: string, endDate: string): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/inventory_logs/?startDate=${startDate}&endDate=${endDate}`, {
        method: 'GET',
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export function useInventoryLogs(startDate: string, endDate: string): UseQueryResult<any, unknown> {
    return useQuery(['inventoryLogs'], async () => {
        return await getInventoryLogs(startDate, endDate);
    });
}
```