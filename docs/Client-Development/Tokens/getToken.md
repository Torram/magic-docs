File with functions to get _token_ from _localStorage_ and updates token.

### getTokenFromLocalStorage
Function that returns stored token and difference in time.

### updateToken
Function that returns an updated token from server.

### getToken
Function that returns a stored token and a new updated token if stored token is not valid anymore.

```tsx
import { differenceInMinutes, parseISO } from 'date-fns';
import { getTokenAndRefreshAuthHeaders } from '../functions/sharedHeaders';
import { localStorageItems } from './localStorageItems';
import { basePath } from '../config/basePath';
import { saveTokenInLocalStorage } from './saveTokenInLocalStorage';
import { getRefreshToken } from './getRefreshToken';

function getTokenFromLocalStorage(): [string, number] {
    const token = localStorage.getItem(localStorageItems.token) ?? '';
    const tokenDateString = localStorage.getItem(localStorageItems.tokenDate) ?? '';
    const tokenCreationDate = parseISO(tokenDateString);
    const today = new Date();
    const difference = differenceInMinutes(today, tokenCreationDate);
    return [token, difference];
}

async function updateToken(currentToken: string, refreshToken: string): Promise<any> {
    const response = await fetch(`${basePath}/auth/token`, {
        method: 'GET',
        headers: getTokenAndRefreshAuthHeaders(currentToken, refreshToken)
    });

    const { token } = await response.json();
    saveTokenInLocalStorage(token);
    return token;
}

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