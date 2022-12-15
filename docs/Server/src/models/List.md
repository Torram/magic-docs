Structure for a list object (binder) in the database.

```tsx
import mongoose, { Schema, Types } from 'mongoose';
import { OrderItemType } from '../databasetypes/OrderItemType';

export interface ListModelType {
    name: string;
    cards: Types.ObjectId[];
    orderItems: OrderItemType[];
}

const ListSchema = new Schema({
    name: { type: String, unique: true, required: true, dropDups: true },
    cards: [{ type: Schema.Types.ObjectId, ref: 'Card' }],
    orderItems: [{ value: Number, card: { type: Schema.Types.ObjectId, ref: 'Card' } }]
});

const List = mongoose.model<ListModelType>('List', ListSchema);
export default List;
```