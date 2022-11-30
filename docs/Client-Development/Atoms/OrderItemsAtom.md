This function is used for order multiple items inside lists (binders).

It's imported from _Recoil_ library. The type of data that uses, is _OrderItemType_, defined in **[types](../types)** folder.

```tsx
import { atom } from 'recoil';
import { OrderItemType } from '../types/OrderItemType';

export const orderItemsAtom = atom<OrderItemType[]>({
    key: 'values',
    default: []
});
```