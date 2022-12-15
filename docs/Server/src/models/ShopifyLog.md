Structure for a ShopifyLog in the database.

```tsx
import mongoose, { Schema } from 'mongoose';

export interface ShopifyLogModelType {
    creationDate: Date;
    changes: [string];
}

const ShopifyLogSchema = new Schema({
    creationDate: Date,
    changes: [String]
});

const ShopifyLog = mongoose.model<ShopifyLogModelType>('ShopifyLog', ShopifyLogSchema);
export default ShopifyLog;
```