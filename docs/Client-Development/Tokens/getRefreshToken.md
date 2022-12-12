File with functions to get _refreshToken_ from _localStorage_ and updates refreshToken.

### getRefreshTokenFromLocalStorage
Function that returns stored refreshToken and difference in time.

### updateRefreshToken
Function that saves an updated token and refreshToken from server into localStorage.

### getRefreshToken
Function that returns a stored refreshToken or a new updated refreshToken if stored refreshToken has 25 to 30 days from created.

```tsx
import { basePath } from '../config/basePath';
import { getRefreshAuthHeaders } from '../functions/sharedHeaders';
import { saveTokenInLocalStorage } from './saveTokenInLocalStorage';
import { saveRefreshTokenInLocalStorage } from './saveRefreshTokenInLocalStorage';
import { tokenErrors } from './tokenErrors';
import { localStorageItems } from './localStorageItems';
import { differenceInMinutes, parseISO } from 'date-fns';

function getRefreshTokenFromLocalStorage(): [string, number] {
    const refreshToken = localStorage.getItem(localStorageItems.refreshToken) ?? '';
    const refreshTokenDateString = localStorage.getItem(localStorageItems.refreshTokenDate) ?? '';
    const tokenCreationDate = parseISO(refreshTokenDateString);
    const today = new Date();
    const difference = differenceInMinutes(today, tokenCreationDate);
    return [refreshToken, difference];
}

async function updateRefreshToken(currentRefreshToken: string): Promise<any> {
    const response = await fetch(`${basePath}/auth/refresh_token`, {
        method: 'GET',
        headers: getRefreshAuthHeaders(currentRefreshToken)
    });

    const { token, refreshToken } = await response.json();

    saveTokenInLocalStorage(token);
    saveRefreshTokenInLocalStorage(refreshToken);

    return refreshToken;
}

export async function getRefreshToken(): Promise<any> {
    const [refreshToken, difference] = getRefreshTokenFromLocalStorage();

    if (refreshToken != null && difference < 36000) {
        return refreshToken;
    } else if (refreshToken != null && difference < 43200) {
        return await updateRefreshToken(refreshToken);
    } else {
        throw Error(tokenErrors.noRefreshToken);
    }
}
```