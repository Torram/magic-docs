File with functions to get the _token_ from the _localStorage_ and updates the token.

### getTokenFromLocalStorage
Function that returns the stored token and difference in time.
```tsx
import { differenceInMinutes, parseISO } from 'date-fns';
import { localStorageItems } from './localStorageItems';

function getTokenFromLocalStorage(): [string, number] {
    const token = localStorage.getItem(localStorageItems.token) ?? '';
    const tokenDateString = localStorage.getItem(localStorageItems.tokenDate) ?? '';
    const tokenCreationDate = parseISO(tokenDateString);
    const today = new Date();
    const difference = differenceInMinutes(today, tokenCreationDate);
    return [token, difference];
}
```
### updateToken
Function that returns an updated token from the server.
```tsx
import { getTokenAndRefreshAuthHeaders } from '../functions/sharedHeaders';
import { saveTokenInLocalStorage } from './saveTokenInLocalStorage';
import { basePath } from '../config/basePath';

async function updateToken(currentToken: string, refreshToken: string): Promise<any> {
    const response = await fetch(`${basePath}/auth/token`, {
        method: 'GET',
        headers: getTokenAndRefreshAuthHeaders(currentToken, refreshToken)
    });

    const { token } = await response.json();
    saveTokenInLocalStorage(token);
    return token;
}
```
### getToken
Function that returns a stored token and a newly updated token if the stored token is not valid anymore.

```tsx
import { getRefreshToken } from './getRefreshToken';

export async function getToken(): Promise<any> {
    const [token, difference] = getTokenFromLocalStorage();

    if (token != null && difference < 8640) {
        return token;
    } else {
        const refreshToken = await getRefreshToken();
        return await updateToken(token, refreshToken);
    }
}
```