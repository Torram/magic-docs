---
sidebar_position: 5
---

## starCityCodes.job
Function that uses _cron_ library to schedule inventory update of Shopify e-commerce at 00:00 o'clock. We obtain all of the cards in sets and lists, update prices and finally, update Shopify inventory.

```tsx
import cron from 'node-cron';
import { getAllSets } from '../controllers/SetsController';
import { getBoxesForSet } from '../controllers/BoxesController';
import { getAllLists } from '../controllers/ListsController';
import { updatePricesForCardsInSet, updatePricesForCardsInList } from '../controllers/CardsController';
import { syncShopifyWithSetState, syncShopifyWithListState } from '../controllers/ShopifyController';

export default function updateShopify(): void {
    cron.schedule('0 0 * * *', async function () {
        const sets = await getAllSets();
        for (const set of sets) {
            const boxesForSet = await getBoxesForSet(set._id);
            if (set.code.length === 3 && boxesForSet.length > 0 && set.cardsReady) {
                await updatePricesForCardsInSet(set._id);
                await syncShopifyWithSetState(set._id, true);
                console.log(`Shopify updated for set ${set.name}`);
            }
        }

        const lists = await getAllLists();
        for (const list of lists) {
            await updatePricesForCardsInList(list._id);
            await syncShopifyWithListState(list._id, true);
            console.log(`Shopify updated for list ${list.name}`);
        }
    });
}
```