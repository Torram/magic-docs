This custom hook creates a new box for the provided set.

```ts
import { useMutation, useQueryClient, UseMutationResult } from 'react-query';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { basePath } from '../../config/basePath';
import { getToken } from '../../tokens/getToken';

async function createBoxForSet(setID: string): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/boxes/`, {
        mode: 'cors',
        method: 'POST',
        body: JSON.stringify({
            setID
        }),
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export function useCreateBoxForSet(): UseMutationResult<any, unknown, string, unknown> {
    const queryClient = useQueryClient();

    return useMutation(createBoxForSet, {
        onSuccess: async () => {
            return await queryClient.invalidateQueries('boxes');
        }
    });
}
```