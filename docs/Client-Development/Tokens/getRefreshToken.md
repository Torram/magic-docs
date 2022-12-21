File with functions to get _refreshToken_ from the _localStorage_ and updates the refreshToken.

### getRefreshTokenFromLocalStorage
Function that returns the stored refreshToken and the difference in time.
```tsx
import { differenceInMinutes, parseISO } from 'date-fns';
import { localStorageItems } from './localStorageItems';

function getRefreshTokenFromLocalStorage(): [string, number] {
    const refreshToken = localStorage.getItem(localStorageItems.refreshToken) ?? '';
    const refreshTokenDateString = localStorage.getItem(localStorageItems.refreshTokenDate) ?? '';
    const tokenCreationDate = parseISO(refreshTokenDateString);
    const today = new Date();
    const difference = differenceInMinutes(today, tokenCreationDate);
    return [refreshToken, difference];
}
```
### updateRefreshToken
Function that saves an updated token and refreshToken from the server into the localStorage.
```tsx
import { saveTokenInLocalStorage } from './saveTokenInLocalStorage';
import { getRefreshAuthHeaders } from '../functions/sharedHeaders';
import { basePath } from '../config/basePath';
import { saveRefreshTokenInLocalStorage } from './saveRefreshTokenInLocalStorage';

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
```
### getRefreshToken
Function that returns a stored refreshToken or a newly updated refreshToken if the stored refreshToken has 25 to 30 days from being created.

```tsx
import { tokenErrors } from './tokenErrors';

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