### getAllLists
Function that returns an array of all lists (binders).
```tsx
import List, { ListModelType } from '../models/List';
import mongoose, { Types } from 'mongoose';

export async function getAllLists(): Promise<
    Array<mongoose.Document<unknown, any, ListModelType> & ListModelType & { _id: Types.ObjectId }>
    > {
    return await List.find({}, '_id name').exec();
}
```

### createList
Function that saves a new list (binder).
```tsx
import List, { ListModelType } from '../models/List';
import mongoose, { Types } from 'mongoose';

export async function createList(
    name: string
): Promise<mongoose.Document<unknown, any, ListModelType> & ListModelType & { _id: Types.ObjectId }> {
    return await List.create({
        name,
        cards: []
    });
}
```

### getList
This function returns a list (binder) with the specified id.
```tsx
import List, { ListModelType } from '../models/List';
import mongoose, { Types } from 'mongoose';

export async function getList(
    id: Types.ObjectId
): Promise<(mongoose.Document<unknown, any, ListModelType> & ListModelType & { _id: Types.ObjectId }) | null> {
    const query = List.findById(id);
    return await query.exec();
}
```

### updateList
Function that updates the cards in a list (binder).
```tsx
import List, { ListModelType } from '../models/List';
import mongoose, { Types } from 'mongoose';

export async function updateList(
    id: Types.ObjectId,
    cards: string[]
): Promise<(mongoose.Document<unknown, any, ListModelType> & ListModelType & { _id: Types.ObjectId }) | null> {
    const list = await getList(id);
    const cardsId = cards.map((card) => new mongoose.Types.ObjectId(card));
    if (list != null) {
        list.cards = list.cards.concat(cardsId);
        return await list.save();
    }

    return list;
}
```

### verifyPosition
Function that checks a card's position in a page of a list (binder).
```tsx
function verifyPosition(value: number): boolean {
    const valueString = value.toString();
    const valueStringArray = valueString.split('.');
    const position = parseInt(valueStringArray[1]);

    if ((!isNaN(position) && position > -1 && position < 10) || value === 0) {
        return true;
    } else {
        return false;
    }
}
```

### checkIfOrderItemIsInList
Function that searches an **_[OrderItem](../serverTypes)_** in a list (binder).
```tsx
import { ListType } from '../servertypes/ListType';
import { OrderItemType } from '../servertypes/OrderItemType';

async function checkIfOrderItemIsInList(list: ListType, pageValue: number): Promise<OrderItemType | undefined> {
    return list.orderItems.find((orderItem) => orderItem.value === pageValue);
}
```

### checkIfCardHasOrderItem
Function that returns an **_[OrderItem](../serverTypes)_** object, if given card exist in list (binder).
```tsx
import List, { ListModelType } from '../models/List';
import mongoose, { Types } from 'mongoose';
import { OrderItemType } from '../servertypes/OrderItemType';

async function checkIfCardHasOrderItem(list: ListType, card: Types.ObjectId): Promise<OrderItemType | undefined> {
    return list.orderItems.find((orderItem) => orderItem.card?.toString() === card.toString());
}
```

### createOrderItem
Function that creates an **_[OrderItem](../serverTypes)_** of the given card, in the specified list.
```tsx
import List, { ListModelType } from '../models/List';
import mongoose, { Types } from 'mongoose';

export async function createOrderItem(
    id: Types.ObjectId,
    cardID: Types.ObjectId,
    pageValue: number
): Promise<(mongoose.Document<unknown, any, ListModelType> & ListModelType & { _id: Types.ObjectId }) | undefined> {
    const list = await getList(id);
    const isValidPageValue = verifyPosition(pageValue);
    let newOrderItem = { value: 0, card: cardID };

    if (isValidPageValue && list != null) {
        const existingOrderItem = await checkIfOrderItemIsInList(list, pageValue);

        if (existingOrderItem != null) {
            existingOrderItem.value = 0;
        }

        newOrderItem = {
            value: pageValue,
            card: cardID
        };

        list.orderItems.push(newOrderItem);
        return await list.save();
    }
}
```

### updateOrderItem
Function that updates an **_[OrderItem](../serverTypes)_** values.
```tsx
import mongoose, { Types } from 'mongoose';
import List, { ListModelType } from '../models/List';

export async function updateOrderItem(
    id: Types.ObjectId,
    value: number,
    listID: Types.ObjectId
): Promise<(mongoose.Document<unknown, any, ListModelType> & ListModelType & { _id: Types.ObjectId }) | undefined> {
    const list = await getList(listID);
    const isValidPageValue = verifyPosition(value);

    if (isValidPageValue && list != null) {
        const orderItemToUpdate = list?.orderItems.find((orderitem) => {
            return orderitem._id === id;
        });
        const existingOrderItem = await checkIfOrderItemIsInList(list, value);

        if (existingOrderItem != null) {
            existingOrderItem.value = 0;
        }

        if (orderItemToUpdate != null) orderItemToUpdate.value = value;
        return await list.save();
    }
}
```

### updateOrderItems
Function that updates multple **_[OrderItems](../serverTypes)_** values in a list.
```tsx
import List, { ListModelType } from '../models/List';
import mongoose, { Types } from 'mongoose';
import { OrderItemType } from '../servertypes/OrderItemType';

export async function updateOrderItems(
    values: OrderItemType[],
    listID: Types.ObjectId
): Promise<(mongoose.Document<unknown, any, ListModelType> & ListModelType & { _id: Types.ObjectId }) | undefined> {
    const list = await getList(listID);

    if (list != null) {
        const filteredValues = values.filter(
            (item, index, valuesArray) =>
                index === valuesArray.findIndex((orderItem) => orderItem.value === item.value && orderItem.card === item.card)
        );

        for (const value of filteredValues) {
            const isValidPageValue = verifyPosition(value.value);

            if (isValidPageValue) {
                const cardWithOrderItem = await checkIfCardHasOrderItem(list, value.card);
                const existingOrderItem = await checkIfOrderItemIsInList(list, value.value);

                if (cardWithOrderItem != null) {
                    if (existingOrderItem != null) {
                        existingOrderItem.value = 0;
                    }

                    const item = list.orderItems.find((orderItem) => {
                        return orderItem._id === cardWithOrderItem._id;
                    });

                    if (item != null) {
                        item.value = value.value;
                    }
                } else {
                    if (existingOrderItem != null) {
                        existingOrderItem.value = 0;
                    }

                    const newOrderItem = {
                        value: value.value,
                        card: value.card
                    };
                    list?.orderItems.push(newOrderItem);
                }

                if (value === filteredValues[filteredValues.length - 1]) {
                    return await list.save();
                }
            }
        }
    }

    return;
}
```