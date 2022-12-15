Structure for a card object in the database.

```tsx
import mongoose, { Schema, Types } from 'mongoose';

export interface CardModelType {
    name: string;
    mtgName: string;
    prices: [Types.ObjectId];
    imageURL: string;
    tags: string;
    body: string;
    handle: string;
    set: Types.ObjectId;
    mtgID: string;
    number: string;
    colors: [string];
    types: [string];
    legalities: [string];
    cmc: number;
    foil: boolean;
    productID: string;
    productVariantID: string;
    inventoryLevelID: string;
    sku: string;
}

const CardSchema = new Schema({
    name: String,
    mtgName: String,
    prices: [{ type: Schema.Types.ObjectId, ref: 'Price' }],
    imageURL: String,
    tags: String,
    body: String,
    handle: String,
    set: { type: Schema.Types.ObjectId, ref: 'Set' },
    mtgID: String,
    number: String,
    colors: [{ type: String }],
    types: [{ type: String }],
    legalities: [{ type: String }],
    cmc: Number,
    foil: Boolean,
    productID: String,
    productVariantID: String,
    inventoryLevelID: String,
    sku: String
});

const Card = mongoose.model<CardModelType>('Card', CardSchema);
export default Card;
```