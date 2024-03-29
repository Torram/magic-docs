---
sidebar_position: 2
---

Component to handle multiple _orderItem_ changes.

## OrderItemsAtom

This function is used for order multiple items inside lists (binders).

It's imported from the _Recoil_ library. The type of data that uses, is _OrderItemType_, defined in the **[types](./types)** folder.

```tsx
import { atom } from "recoil";
import { OrderItemType } from "../types/OrderItemType";

export const orderItemsAtom = atom<OrderItemType[]>({
  key: "values",
  default: [],
});
```
