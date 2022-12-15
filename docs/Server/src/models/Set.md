Structure for a set in the database.

```tsx
import mongoose, { Schema } from 'mongoose';

export interface SetModelType {
    name: string;
    code: string;
    creationDate: Date;
    releaseDate: Date;
    cardsReady: boolean;
}

const SetSchema = new Schema({
    name: { type: String, unique: true, required: true, dropDups: true },
    code: String,
    creationDate: Date,
    releaseDate: Date,
    cardsReady: Boolean
});

const Set = mongoose.model<SetModelType>('Set', SetSchema);
export default Set;
```