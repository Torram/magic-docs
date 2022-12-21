Function that saves a given _refreshToken_ and the actual date in **_[localStorageItems]_** into _localStorage_.

```tsx
import { localStorageItems } from './localStorageItems';
import { formatISO } from 'date-fns';

export function saveRefreshTokenInLocalStorage(refreshToken: string): void {
    localStorage.setItem(localStorageItems.refreshToken, refreshToken);
    const date = formatISO(new Date());
    localStorage.setItem(localStorageItems.refreshTokenDate, date);
}
```