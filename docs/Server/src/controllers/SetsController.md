### getSetsWithCards
Function that returns an array of sets with cards already loaded.
```tsx
import { Types, Document } from 'mongoose';
import Set, { SetModelType } from '../models/Set';

export async function getSetsWithCards(): Promise<Array<Document<unknown, any, SetModelType> & SetModelType & { _id: Types.ObjectId }>> {
    return await Set.find({ cardsReady: true }).exec();
}
```

### createSet
This function creates a **_[Set](../models/Set)_** object, from a set code and a pre fetched set.
```tsx
import { SetType } from '../servertypes/SetType';
import Set, { SetModelType } from '../models/Set';
import { Types, Document } from 'mongoose';
import { getMTGSet, getAllMTGSets } from './MTGController';

export async function createSet(
    code: string,
    preFetchedSet: SetType
): Promise<(Document<unknown, any, SetModelType> & SetModelType & { _id: Types.ObjectId }) | null> {
    const mtgSet = preFetchedSet ?? (await getMTGSet(code));
    const setAlreadyExists = await Set.exists({ name: mtgSet.name });

    if (setAlreadyExists != null) {
        return await getSetWithMTGCode(mtgSet.code);
    } else {
        return await Set.create({
            name: mtgSet.name,
            code: mtgSet.code,
            creationDate: Date(),
            releaseDate: mtgSet.releaseDate,
            cardsReady: false
        });
    }
}
```

### getSet
Function that returns a specific set by id.
```tsx
import { Types, Document } from 'mongoose';
import Set, { SetModelType } from '../models/Set';

export async function getSet(
    id: Types.ObjectId
): Promise<(Document<unknown, any, SetModelType> & SetModelType & { _id: Types.ObjectId }) | null> {
    return await Set.findById(id).exec();
}
```

### getSetWithMTGCode
Function that returns a specific set by MTG code.
```tsx
import Set, { SetModelType } from '../models/Set';
import { Types, Document } from 'mongoose';

async function getSetWithMTGCode(
    code: string
): Promise<(Document<unknown, any, SetModelType> & SetModelType & { _id: Types.ObjectId }) | null> {
    const query = Set.findOne({
        code: code.toUpperCase()
    });
    return await query.exec();
}
```

### reloadAllSetsFromMTG
Function that reloads all sets in database, to add new released sets.
```tsx
import { getMTGSet, getAllMTGSets } from './MTGController';
import { Types, Document } from 'mongoose';
import Set, { SetModelType } from '../models/Set';

export async function reloadAllSetsFromMTG(): Promise<
    Array<(Document<unknown, any, SetModelType> & SetModelType & { _id: Types.ObjectId }) | null>
    > {
    const allMTGSets = await getAllMTGSets();
    const creationPromises = allMTGSets.map(async (mtgSet: SetType) => {
        return await createSet('', mtgSet);
    });
    return await Promise.all(creationPromises);
}
```

### getAllSets
This function returns all of the **_[Set](../models/Set)_** objects.
```tsx
import { Types, Document } from 'mongoose';
import Set, { SetModelType } from '../models/Set';

export async function getAllSets(): Promise<Array<Document<unknown, any, SetModelType> & SetModelType & { _id: Types.ObjectId }>> {
    return await Set.find({}).exec();
}
```

### getAllSetNames
Function that returns an array of all of the names of sets in database
```tsx
import Set, { SetModelType } from '../models/Set';
import { Types, Document } from 'mongoose';

export async function getAllSetNames(): Promise<Array<Document<unknown, any, SetModelType> & SetModelType & { _id: Types.ObjectId }>> {
    return await Set.find({}, '_id name code').exec();
}
```

### createCards
Function that populates a set with all of its cards.
```tsx
import { createCardsForSet } from './CardsController';
import { Types, Document } from 'mongoose';

export async function createCards(
    setID: Types.ObjectId
): Promise<(Document<unknown, any, SetModelType> & SetModelType & { _id: Types.ObjectId }) | undefined> {
    const set = await getSet(setID);

    if (set != null && set.cardsReady) {
        return set;
    } else if (set != null) {
        await createCardsForSet(set);
        set.cardsReady = true;
        return await set.save();
    }
}
```