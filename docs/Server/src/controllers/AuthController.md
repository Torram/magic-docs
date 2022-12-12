## interface
```tsx
interface Tokens {
    token: string;
    refreshToken: string;
}
```
## Authentication

### signUp
Function that register a new user, if the admin key exists and is correct.
```tsx
import bcrypt from 'bcryptjs';
import User, { UserModelType } from '../models/User';
import { generateTokens } from './TokenController';

export async function signUp(
    information: any,
    password: string,
    key: string
): Promise<{ user: { name: string; lastname: string; email: string; type: string }; token: string; refreshToken: string }> {
    const user = await User.create({
        ...information,
        password: bcrypt.hashSync(password),
        type: getUserType(key)
    });

    const tokens = await generateTokens(user.email, user.type, user._id.toString());
    return generateDataResponse(tokens, user);
}
```

### signIn
Function that log in a user if email and password matches with a register in database.
```tsx
import { generateTokens } from './TokenController';

export async function signIn(
    email: string,
    password: string
): Promise<{ user: { name: string; lastname: string; email: string; type: string }; token: string; refreshToken: string }> {
    const user = await getUserByMail(email, true);

    if (user == null) {
        throw Error('A user with that email address does not exist.');
    }

    const isPasswordCorrect = await checkPassword(user, password);

    if (isPasswordCorrect) {
        const tokens = await generateTokens(user.email, user.type, user._id.toString());
        return generateDataResponse(tokens, user);
    } else {
        throw Error('Wrong credentials');
    }
}
```
## Queries
Function that gets a **[user]('../models/user)** registry from database, where id matches.
### getUserById
```tsx
import User, { UserModelType } from '../models/User';
import { Document, Types } from 'mongoose';

export async function getUserByID(
    id: Types.ObjectId
): Promise<(Document<unknown, any, UserModelType> & UserModelType & { _id: Types.ObjectId }) | null> {
    return await User.findById(id).exec();
}
```
### getUserByMail
Function that gets a **[user]('../models/user)** registry from database, where email matches.
```tsx
import User, { UserModelType } from '../models/User';
import { Document, Types } from 'mongoose';

export async function getUserByMail(
    email: string,
    includePassword = false
): Promise<(Document<unknown, any, UserModelType> & UserModelType & { _id: Types.ObjectId }) | null> {
    const query = User.findOne({
        email
    });

    if (!includePassword) {
        await query.select('-password');
    }

    return await query.clone().exec();
}

```
## Helpers

### generateDataResponse
Helper function that instances **[user]('../models/user')** data structure for API response in this controller.
```tsx
import { UserType } from '../servertypes/UserType';

export function generateDataResponse(
    tokens: Tokens,
    user: UserType
): { user: { name: string; lastname: string; email: string; type: string }; token: string; refreshToken: string } {
    return {
        ...tokens,
        user: {
            name: user.name,
            lastname: user.lastname,
            email: user.email,
            type: user.type
        }
    };
}
```

### getUserType
Helper function that returns the type of privilegies of a user.
```tsx
import { ADMIN_KEY } from '../constants/constants';

function getUserType(key: string): 'admin' | 'normal' {
    return key === ADMIN_KEY ? 'admin' : 'normal';
}
```

## Credentials Verification

### checkPassword
Function used for credentials verification.
```tsx
import bcrypt from 'bcryptjs';

async function checkPassword(user: UserType, password: string): Promise<boolean> {
    return await bcrypt.compare(password, user.password);
}
```