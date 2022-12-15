Structure for a user in the database.

```tsx
import mongoose, { Schema } from 'mongoose';

export interface UserModelType {
    name: string;
    lastname: string;
    email: string;
    password: string;
    type: string;
}

const UserSchema = new Schema({
    name: String,
    lastname: String,
    email: { type: String, unique: true, required: true, dropDups: true },
    password: String,
    type: String
});

const User = mongoose.model<UserModelType>('User', UserSchema);
export default User;
```