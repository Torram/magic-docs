### getAllBoxes
Function that returns all boxes for all sets.
```tsx
import Box, { BoxModelType } from '../models/Box';
import { Types, Document } from 'mongoose';

export async function getAllBoxes(): Promise<Array<Document<unknown, any, BoxModelType> & BoxModelType & { _id: Types.ObjectId }>> {
    return await Box.find({}).exec();
}
```
### getBoxesForSet
Function that returns all of the boxes for a specified set. 
```tsx
import Box, { BoxModelType } from '../models/Box';
import { Types, Document } from 'mongoose';

export async function getBoxesForSet(
    setID: Types.ObjectId
): Promise<Array<Document<unknown, any, BoxModelType> & BoxModelType & { _id: Types.ObjectId }>> {
    return await Box.find({ set: setID }).exec();
}
```
### createBox
Function that gets an array of boxes of a set for set the next number of the new box, then, creates a new box and adds it to set.
```tsx
import Box, { BoxModelType } from '../models/Box';
import { getSet } from './SetsController';
import { Types, Document } from 'mongoose';

export async function createBox(
    setID: Types.ObjectId
): Promise<(Document<unknown, any, BoxModelType> & BoxModelType & { _id: Types.ObjectId }) | undefined> {
    const set = await getSet(setID);
    const boxesForSet = await getBoxesForSet(setID);

    const nextBoxNumber = boxesForSet.length + 1;

    if (set != null) {
        const newBoxName = nextBoxNumber < 10 ? `${set.code}-0${nextBoxNumber}` : `${set.code}-${nextBoxNumber}`;

        return await Box.create({
            name: newBoxName,
            set: set?.id.toString(),
            inventoryItems: []
        });
    }

    return;
}
```
### getBox
Function that returns a specific box of a set.
```tsx
import Box, { BoxModelType } from '../models/Box';
import { Types, Document } from 'mongoose';

export async function getBox(
    id: Types.ObjectId
): Promise<(Document<unknown, any, BoxModelType> & BoxModelType & { _id: Types.ObjectId }) | null> {
    const query = Box.findById(id);
    return await query.exec();
}
```