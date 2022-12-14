Structure for a InventoryLog object in database.

```tsx
import mongoose, { Schema } from 'mongoose';

export interface InventoryLogModelType {
    cardName: string;
    boxName: string;
    listName: string;
    quantity: number;
    userName: string;
    creationDate: Date;
}

const InventoryLogSchema = new Schema({
    cardName: String,
    boxName: String,
    listName: String,
    quantity: { type: Number, required: true },
    userName: String,
    creationDate: { type: Date, required: true }
});

const InventoryLog = mongoose.model<InventoryLogModelType>('InventoryLog', InventoryLogSchema);
export default InventoryLog;
```