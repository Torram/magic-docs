Structure for a InventoryItem object in server.

```tsx
import mongoose, { Schema, Types } from 'mongoose';

export interface InventoryItemModelType {
    card: Types.ObjectId;
    quantity: number;
    box: Types.ObjectId;
    list: Types.ObjectId;
}

const InventoryItemSchema = new Schema({
    card: { type: Schema.Types.ObjectId, ref: 'Card', required: true },
    quantity: Number,
    box: { type: Schema.Types.ObjectId, ref: 'Box' },
    list: { type: Schema.Types.ObjectId, ref: 'List' }
});

const InventoryItem = mongoose.model<InventoryItemModelType>('InventoryItem', InventoryItemSchema);
export default InventoryItem;
```