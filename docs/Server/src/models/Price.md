Structure for a price object in the database.

```tsx
import mongoose, { Schema } from 'mongoose';

export interface PriceModelType {
    value: number;
    creationDate: Date;
}

const PriceSchema = new Schema({
    value: Number,
    creationDate: Date
});

const Price = mongoose.model<PriceModelType>('Price', PriceSchema);
export default Price;
```