This custom hook is used for the creation of a new list (binder).

```tsx
import { getToken } from '../../tokens/getToken';
import { basePath } from '../../config/basePath';
import { getTokenAuthHeaders } from '../../functions/sharedHeaders';
import { useMutation, useQueryClient, UseMutationResult } from 'react-query';

async function createList(name: string): Promise<any> {
    const token = await getToken();
    const response = await fetch(`${basePath}/lists/`, {
        method: 'POST',
        body: JSON.stringify({ name }),
        headers: getTokenAuthHeaders(token)
    });

    return await response.json();
}

export default function useCreateList(): UseMutationResult<any, unknown, string, unknown> {
    const queryClient = useQueryClient();

    return useMutation(createList, {
        onSuccess: async () => {
            return await queryClient.invalidateQueries('lists');
        }
    });
}
```