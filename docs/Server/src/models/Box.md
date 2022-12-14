Structure for a box in database.

```tsx
import mongoose, { Schema, Types } from 'mongoose';

export interface BoxModelType {
    name: string;
    set: Types.ObjectId;
}

const BoxSchema = new Schema({
    name: { type: String, unique: true, required: true, dropDups: true },
    set: { type: Schema.Types.ObjectId, ref: 'Set', required: true }
});

const Box = mongoose.model<BoxModelType>('Box', BoxSchema);
export default Box;
```