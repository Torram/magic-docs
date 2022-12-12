### generateToken
Function that generates a new token, that is handled by jsonwebtoken library.
```tsx
import jwt from 'jsonwebtoken';
import { API_SECRET, API_SECRET_REFRESH, TOKEN_EXP, REFRESH_TOKEN_EXP } from '../constants/constants';

export function generateToken(username: string, type: string, id: string): string {
    return jwt.sign(
        {
            username,
            type,
            id
        },
        API_SECRET,
        {
            expiresIn: TOKEN_EXP
        }
    );
}
```

### generateRefreshToken
Function that renews the existing session token.
```tsx
import jwt from 'jsonwebtoken';
import { API_SECRET, API_SECRET_REFRESH, TOKEN_EXP, REFRESH_TOKEN_EXP } from '../constants/constants';

function generateRefreshToken(username: string, type: string, id: string): string {
    return jwt.sign(
        {
            username,
            type,
            id
        },
        API_SECRET_REFRESH,
        {
            expiresIn: REFRESH_TOKEN_EXP
        }
    );
}
```

### generateTokens
Function that calls function that generates new and renewed tokens.
```tsx
export async function generateTokens(email: string, type: string, id: string): Promise<{ token: string; refreshToken: string }> {
    return {
        token: generateToken(email, type, id),
        refreshToken: generateRefreshToken(email, type, id)
    };
}
```

### verifyAnyToken
Function that checks if a provided token is valid.
```tsx
import jwt from 'jsonwebtoken';

async function verifyAnyToken(token: any, secret: string): Promise<void> {
    return await new Promise((resolve, reject) => {
        jwt.verify(token, secret, (error: any) => {
            if (error != null) {
                reject(error);
            } else {
                resolve(token);
            }
        });
    });
}
```

### verifyToken
Function that calls _verifyAnyToken_ function with main token.
```tsx
async function verifyToken(token: string): Promise<any> {
    return await verifyAnyToken(token, API_SECRET);
}
```

### verifyRefreshToken
Function that calls _verifyAnyToken_ function with renewed token.
```tsx
export async function verifyRefreshToken(token: string): Promise<any> {
    return await verifyAnyToken(token, API_SECRET_REFRESH);
}
```

### getNormalizedToken
Function that normalizes the provided token.
```tsx
async function getNormalizedToken(token: any): Promise<any> {
    if (token === null || token === undefined) {
        throw Error('Auth token is not supplied');
    } else if (String(token).startsWith('Bearer ')) {
        token = token.slice(7, token.length); // Remove Bearer from string
    }

    if (token != null && token.toString().length > 7) {
        return token;
    } else {
        throw Error('Auth token is not supplied');
    }
}
```

### getTokenFromRequest
Function that gets the provided token from a web request.
```tsx
import { Request, Response, NextFunction } from 'express';

export async function getTokenFromRequest(req: Request): Promise<string> {
    const token = req.body.token ?? req.headers['x-access-token'] ?? req.headers.authorization ?? req.params.token;
    return await getNormalizedToken(token);
}
```

### getRefreshTokenFromRequest
Function that gets the provided renewed token from a web request.
```tsx
import { Request, Response, NextFunction } from 'express';

export async function getRefreshTokenFromRequest(req: Request): Promise<any> {
    const refreshToken =
        req.body.refreshToken ?? req.headers['x-refresh-token'] ?? req.headers['authorization-refresh'] ?? req.params.refreshToken;
    return await getNormalizedToken(refreshToken);
}
```

### isAValidToken
Function that gets the provided token from a web request, then, checks if this token is valid. 
```tsx
import { Request, Response, NextFunction } from 'express';

export async function isAValidToken(req: Request, res: Response, next: NextFunction): Promise<void> {
    const token = await getTokenFromRequest(req);
    await verifyToken(token);
    next();
}
```