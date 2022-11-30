---
sidebar_position: 9
---

Custom types for typescript.

### BoxType
```tsx
export interface BoxType {
    name: string;
    set: string;
    _id: string;
}
```

### CardOptionType
```tsx
export interface CardOptionType {
    value: string;
    label: string;
}
```

### CardPositionType
```tsx
export interface CardPositionType {
    value: number;
    card: string;
}
```

### CardType
```tsx
import { PriceType } from './PriceType';

export interface CardType {
    name: string;
    sku: string;
    imageURL: string;
    prices: PriceType[];
    _id: string;
}
```

### InventoryItemType
```tsx
export interface InventoryItemType {
    _id: string;
    quantity: number;
}
```

### InventoryLogType
```tsx
export interface InventoryLogType {
    _id: string;
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
import { OrderItemType } from './OrderItemType';

export interface ListType {
    _id: string;
    name: string;
    cards: string[];
    orderItems: OrderItemType[];
}
```

### OrderItemType
```tsx
export interface OrderItemType {
    _id?: string;
    value: number;
    card: string;
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
export interface SetType {
    _id: string;
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
    _id: string;
    creationDate: Date;
    changes: string[];
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