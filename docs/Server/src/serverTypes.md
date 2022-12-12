---
sidebar_position: 8
---

Custom types for typescript. 

### ApiCardType
```tsx
export interface ApiCardType {
    name: string;
    number: string;
    colors: [string];
    types: [string];
    legalities: [];
    text: string;
    convertedManaCost: string;
    rarity: string;
    finishes: [string];
    identifiers: { multiverseId: string };
}
```
### BoxType
```tsx
import { Types } from 'mongoose';

export interface BoxType {
    _id: Types.ObjectId;
    name: string;
    set: Types.ObjectId;
}
```

### CardType
```tsx
import { Types, Document } from 'mongoose';

export type CardType = {
    _id: Types.ObjectId;
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
} & Document;
```

### InventoryItemType
```tsx
import { Types, Document } from 'mongoose';

export type InventoryItemType = {
    _id: Types.ObjectId;
    card: Types.ObjectId;
    quantity: number;
    box: Types.ObjectId;
    list: Types.ObjectId;
} & Document;
```

### InventoryLogType
```tsx
export interface InventoryLogType {
    cardName: string;
    boxName: string;
    listName: string;
    quantity: number;
    userName: string;
    creationDate: Date;
}
```

### ListType
```tsx
import { Types } from 'mongoose';
import { OrderItemType } from './OrderItemType';

export interface ListType {
    _id: Types.ObjectId;
    name: string;
    cards: Types.ObjectId[];
    orderItems: OrderItemType[];
}
```

### OrderItemType
```tsx
import { Types } from 'mongoose';

export interface OrderItemType {
    _id?: Types.ObjectId;
    value: number;
    card: Types.ObjectId;
}
```

### PriceType
```tsx
export interface PriceType {
    value: number;
    creationDate: Date;
}
```

### SetType
```tsx
import { Types } from 'mongoose';

export interface SetType {
    _id: Types.ObjectId;
    name: string;
    code: string;
    creationDate: Date;
    releaseDate: Date;
    cardsReady: boolean;
}
```

### ShopifyLogType
```tsx
export interface ShopifyLogType {
    creationDate: Date;
    changes: [string];
}
```

### UserType
```tsx
export interface UserType {
    name: string;
    lastname: string;
    email: string;
    password: string;
    type: string;
}
```

### WebhookEventType
```tsx
export interface WebhookEventType {
    webhookID: string;
    eventTopic: string;
    executionDate: string;
}
```