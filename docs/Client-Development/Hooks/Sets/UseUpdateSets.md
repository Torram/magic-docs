This custom hook updates the list of all sets to get the new releases.

```tsx
import { getToken } from '../../tokens/getToken';
import { basePath } from '../../config/basePath';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { useMutation, useQueryClient, UseMutationResult } from 'react-query';

async function updateSets(): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/sets`, {
        method: 'PUT',
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export function useUpdateSets(): UseMutationResult<any, unknown, void, unknown> {
    const queryClient = useQueryClient();

    return useMutation(
        async () => {
            return await updateSets();
        },
        {
            onSuccess: async () => {
                return await queryClient.invalidateQueries(['sets']);
            }
        }
    );
}
```