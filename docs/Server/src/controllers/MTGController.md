### getMTGSet
Function that returns a set if exists.
```tsx
export async function getMTGSet(code: string): Promise<SetType | undefined> {
    const mtgSets = await getAllMTGSets();
    const set = mtgSets.find((set) => set.code === code);
    return set;
}
```

### getAllMTGSets
Function that fetch all released sets from mtgjson API.
```tsx
import { SetType } from '../servertypes/SetType';
const fetchRetry = require('node-fetch-retry');

export async function getAllMTGSets(): Promise<SetType[]> {
    const url = 'https://mtgjson.com/api/v5/AllPrintings.json';
    const mtgSets: SetType[] = [];

    const response = await fetchRetry(url, {
        method: 'GET',
        retry: 100000000
    });

    const json = await response.json();

    Object.entries(json.data).map((set) => mtgSets.push(set[1] as SetType));

    return mtgSets;
}
```

### getMTGCards
Function that fetch all cards in a provided set.
```tsx
const fetchRetry = require('node-fetch-retry');

export async function getMTGCards(setCode: string): Promise<any> {
    const url = `https://mtgjson.com/api/v5/${setCode}.json`;

    const response = await fetchRetry(url, {
        method: 'GET',
        retry: 100000000
    });

    const json = await response.json();
    return json.data.cards;
}
```