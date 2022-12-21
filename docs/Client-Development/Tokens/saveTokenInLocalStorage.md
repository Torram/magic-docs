Function that saves a given token and the actual date in **_[localStorageItems]_** into _localStorage_.

```tsx
import { localStorageItems } from './localStorageItems';
import { formatISO } from 'date-fns';

export function saveTokenInLocalStorage(token: string): void {
    localStorage.setItem(localStorageItems.token, token);
    const date = formatISO(new Date());
    localStorage.setItem(localStorageItems.tokenDate, date);
}
```