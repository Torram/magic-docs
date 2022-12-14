### createInventoryItem
Function that creates an **_[InventoryItem](../models/InventoryItem)_** object, that stores a card, a container and the quantity of that card. 
```tsx
import { Types, Document } from 'mongoose';
import { InventoryItemType } from '../servertypes/InventoryItemType';

export async function createInventoryItem(
    cardID: Types.ObjectId,
    boxID: Types.ObjectId | null,
    listID: Types.ObjectId | null,
    value: number
): Promise<
    Document<unknown, any, InventoryItemModelType> &
        InventoryItemModelType & {
            _id: Types.ObjectId;
        }
> {
    return await InventoryItem.create({
        card: cardID,
        quantity: value,
        box: boxID,
        list: listID
    });
}
```

### getBoxesForInventoryItems
Function that returns the boxes where given **_[InventoryItem](../models/InventoryItem)_** are.
```tsx
import { Types, Document } from 'mongoose';
import { InventoryItemType } from '../servertypes/InventoryItemType';
import { getBox } from '../controllers/BoxesController';
import { BoxModelType } from '../models/Box';

export async function getBoxesForInventoryItems(
    inventoryItems: InventoryItemType[]
): Promise<Array<(Document<unknown, any, BoxModelType> & BoxModelType & { _id: Types.ObjectId }) | null>> {
    const boxesIDs = inventoryItems.map((item) => item.box).filter((id) => id != null);
    return await Promise.all(boxesIDs.map(async (id) => await getBox(id)));
}
```

### getListsForInventoryItems
Function that returns the lists (binders) where given **_[InventoryItem](../models/InventoryItem)_** are.
```tsx
import { InventoryItemType } from '../servertypes/InventoryItemType';
import { ListModelType } from '../models/List';
import { Types, Document } from 'mongoose';
import { getList } from '../controllers/ListsController';

export async function getListsForInventoryItems(
    inventoryItems: InventoryItemType[]
): Promise<Array<(Document<unknown, any, ListModelType> & ListModelType & { _id: Types.ObjectId }) | null>> {
    const listsIDs = inventoryItems.map((item) => item.list).filter((id) => id != null);
    return await Promise.all(listsIDs.map(async (id) => await getList(id)));
}
```

### updateInventoryItemQuantity
Function that updates the quantity of an **_[InventoryItem](../models/InventoryItem)_**.
```tsx
import { Types, Document } from 'mongoose';
import { InventoryItemType } from '../servertypes/InventoryItemType';
import InventoryItem, { InventoryItemModelType } from '../models/InventoryItem';

export async function updateInventoryItemQuantity(
    id: Types.ObjectId,
    value: number
): Promise<(Document<unknown, any, InventoryItemModelType> & InventoryItemModelType & { _id: Types.ObjectId }) | null> {
    const inventoryItem = await InventoryItem.findById(id).exec();
    if (inventoryItem != null) {
        inventoryItem.quantity = value;
        return await inventoryItem.save();
    }

    return null;
}
```

### getInventoryItemsForCard
Function that returns the **_[InventoryItem](../models/InventoryItem)_** of a card.
```tsx
import { Types, Document } from 'mongoose';
import InventoryItem, { InventoryItemModelType } from '../models/InventoryItem';

export async function getInventoryItemsForCard(
    cardID: Types.ObjectId
): Promise<Array<Document<unknown, any, InventoryItemModelType> & InventoryItemModelType & { _id: Types.ObjectId }>> {
    return await InventoryItem.find({ card: cardID }).exec();
}
```

### getInventoryMaxQuantityForCard
This function returns the current quantity of a card, wherever it is.
```tsx
import { Types, Document } from 'mongoose';

export async function getInventoryMaxQuantityForCard(cardID: Types.ObjectId): Promise<number> {
    const items = await getInventoryItemsForCard(cardID);
    const values = items.map((item) => item.quantity);
    return values.length > 0 ? values.reduce((previousValue, currentValue) => previousValue + currentValue) : 0;
}
```

### getInventoryItemForCardInBox
Function that returns an **_[InventoryItem](../models/InventoryItem)_** object of a card in a box.
```tsx
import { Types, Document } from 'mongoose';
import { InventoryItemType } from '../servertypes/InventoryItemType';
import InventoryItem, { InventoryItemModelType } from '../models/InventoryItem';

export async function getInventoryItemForCardInBox(
    cardID: string,
    boxID: string
): Promise<(Document<unknown, any, InventoryItemModelType> & InventoryItemModelType & { _id: Types.ObjectId }) | null> {
    return await InventoryItem.findOne({ box: boxID, card: cardID }).exec();
}
```

### getInventoryItemForCardInList
Function that returns an **_[InventoryItem](../models/InventoryItem)_** object of a card in a list (binder).
```tsx
import { Types, Document } from 'mongoose';
import { InventoryItemType } from '../servertypes/InventoryItemType';
import InventoryItem, { InventoryItemModelType } from '../models/InventoryItem';

export async function getInventoryItemForCardInList(
    cardID: Types.ObjectId,
    listID: Types.ObjectId
): Promise<(Document<unknown, any, InventoryItemModelType> & InventoryItemModelType & { _id: Types.ObjectId }) | null | undefined> {
    if (cardID != null) {
        const item = await InventoryItem.findOne({ list: listID, card: cardID }).exec();
        return item;
    }
}
```

### getInventoryItemsForCardsInBox
Function that returns an array of **_[InventoryItem](../models/InventoryItem)_** objects of all cards in a box.
```tsx
import { Types, Document } from 'mongoose';
import { InventoryItemType } from '../servertypes/InventoryItemType';
import { CardType } from '../servertypes/CardType';

export async function getInventoryItemsForCardsInBox(
    cards: CardType[],
    boxID: string
): Promise<Array<(Document<unknown, any, InventoryItemModelType> & InventoryItemModelType & { _id: Types.ObjectId }) | null>> {
    return await Promise.all(cards.map(async (card: CardType) => await getInventoryItemForCardInBox(card._id, boxID)));
}
```

### getInventoryItemsForCardsInList
Function that returns an array of **_[InventoryItem](../models/InventoryItem)_** objects of all cards in a _list_ (binder).
```tsx
import { Types, Document } from 'mongoose';
import { InventoryItemType } from '../servertypes/InventoryItemType';
import { CardType } from '../servertypes/CardType';

export async function getInventoryItemsForCardsInList(
    cards: CardType[],
    listID: Types.ObjectId
): Promise<Array<(Document<unknown, any, InventoryItemModelType> & InventoryItemModelType & { _id: Types.ObjectId }) | null | undefined>> {
    return await Promise.all(cards.map(async (card: CardType) => await getInventoryItemForCardInList(card._id, listID)));
}
```
