---
sidebar_position: 1
---

Component that manages all the explorer headers.

## Functions

### getJSONHeaders

This function returns an object with the necessary HTTP header properties.

```tsx
export function getJSONHeaders(): Headers {
    return new Headers({
        'Access-Control-Allow-Origin': '*',
        'Content-Type': 'application/json',
        Accept: 'application/json'
    });
}
```

### getTokenAuthHeaders

Function that gets the current authentication token.

```tsx
export function getTokenAuthHeaders(token: string): Headers {
    const headers = getJSONHeaders();
    headers.append('x-access-token', token);
    return headers;
}
```

### getRefreshAuthHeaders

Function that gets the new authentication token.

```tsx
export function getRefreshAuthHeaders(refreshToken: string): Headers {
    const headers = getJSONHeaders();
    headers.append('x-refresh-token', refreshToken);
    return headers;
}
```

### getTokenAndRefreshAuthHeaders

Function that gets the current and the new authentication tokens.

```tsx
export function getTokenAndRefreshAuthHeaders(token: string, refreshToken: string): Headers {
    const headers = getJSONHeaders();
    headers.append('x-access-token', token);
    headers.append('x-refresh-token', refreshToken);
    return headers;
}
```