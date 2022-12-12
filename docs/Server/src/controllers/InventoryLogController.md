### createInventoryLog
Function that saves an **_[InventoryLog](../models/InventoryLog)_** object with all of the modifications in inventory.
```tsx
import InventoryLog, { InventoryLogModelType } from '../models/InventoryLog';
import { getCardByID } from '../controllers/CardsController';
import { getBox } from '../controllers/BoxesController';
import { getList } from '../controllers/ListsController';
import { getUserByID } from '../controllers/AuthController';
import { Types, Document } from 'mongoose';

export async function createInventoryLog(
    cardID: Types.ObjectId,
    boxID: Types.ObjectId | null,
    listID: Types.ObjectId | null,
    quantity: number,
    userID: Types.ObjectId
): Promise<
    Document<unknown, any, InventoryLogModelType> &
        InventoryLogModelType & {
            _id: Types.ObjectId;
        }
> {
    const card = await getCardByID(cardID);
    let boxName;
    let listName;

    if (boxID != null) {
        const box = await getBox(boxID);
        boxName = box?.name;
    }

    if (listID != null) {
        const list = await getList(listID);
        listName = list?.name;
    }

    const user = await getUserByID(userID);
    const userName = user != null ? `${user.name} ${user.lastname}` : '';

    return await InventoryLog.create({
        cardName: card?.name,
        boxName,
        listName,
        quantity,
        userName,
        creationDate: new Date()
    });
}

```

### getInventoryLogs
Function that returns all of the **_[InventoryLogs](../models/InventoryLog)_** between given dates.
```tsx
import InventoryLog, { InventoryLogModelType } from '../models/InventoryLog';
import { Types, Document } from 'mongoose';

export async function getInventoryLogs(
    startDateString: string,
    endDateString: string
): Promise<Array<Document<unknown, any, InventoryLogModelType> & InventoryLogModelType & { _id: Types.ObjectId }>> {
    const startDate = new Date(startDateString);
    const endDate = new Date(endDateString);

    startDate.setHours(0, 0, 0, 0);
    endDate.setHours(59, 59, 59, 999);

    return await InventoryLog.find({ creationDate: { $gte: startDate, $lt: endDate } })
        .sort('-creationDate')
        .exec();
}
```