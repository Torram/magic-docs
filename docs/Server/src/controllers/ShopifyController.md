### getAllShopifyLogs
Function that returns all of the Shopify logs, sorted by creation date.
```tsx
import ShopifyLog, { ShopifyLogModelType } from '../models/ShopifyLog';
import { Types, Document } from 'mongoose';

export async function getAllShopifyLogs(): Promise<
    Array<
        Document<unknown, any, ShopifyLogModelType> &
            ShopifyLogModelType & {
                _id: Types.ObjectId;
            }
    >
    > {
    return await ShopifyLog.find({}).sort('-creationDate').exec();
}
```

## Shopify as Truth
This functions gets values from Shopify to update system database.
### syncDatabaseWithShopifyState
Function that uses all cards in all sets and gets values for that cards from Shopify e-commerce to update database, then, creates a **[ShopifyLog](../models/ShopifyLog)**.
```tsx
import { Types, Document } from 'mongoose';
import ShopifyLog, { ShopifyLogModelType } from '../models/ShopifyLog';

export async function syncDatabaseWithShopifyState(): Promise<
    Document<unknown, any, ShopifyLogModelType> & ShopifyLogModelType & { _id: Types.ObjectId }
    > {
    const shopifyLog = await ShopifyLog.create({
        creationDate: new Date(),
        changes: []
    });
    const sets = await getSetsWithCards();

    for (const set of sets) {
        const cards = await getFullCardsForSet(set._id);

        for (const card of cards) {
            await syncCardWithShopifyState(card, shopifyLog);
        }
    }

    return await shopifyLog.save();
}
```

### syncCardWithShopifyState
Function that updates quantities from Shopify to the system, adding all changes to specified **[ShopifyLog](../models/ShopifyLog.md)**.
```tsx
import InventoryItem from '../models/InventoryItem';
import { CardType } from '../servertypes/CardType';
import { Types, Document } from 'mongoose';
import { ShopifyLogType } from '../servertypes/ShopifyLogType';

async function syncCardWithShopifyState(card: CardType, shopifyLog: ShopifyLogType): Promise<void> {
    if (card.productID != null && card.productVariantID != null && card.inventoryLevelID != null) {
        const shopifyQuantity = await getCardInventoryLevelInShopify(card); // This value will always be smaller than internalQuantity or equal

        const internalQuantity = await getInventoryMaxQuantityForCard(card._id);
        let differenceInQuantity = internalQuantity - shopifyQuantity;

        if (differenceInQuantity > 0 && shopifyQuantity < internalQuantity) {
            const inventoryItemsModified: InventoryItemType[] = []; // This is a data structure not a card set.
            const inventoryItems = await getInventoryItemsForCard(card._id);

            while (differenceInQuantity > 0) {
                const inventoryItem = inventoryItems.find((item) => item.quantity > 1);

                if (inventoryItem != null) {
                    const newQuantity = inventoryItem.quantity - 1;
                    inventoryItem.quantity = newQuantity;
                    differenceInQuantity -= 1;

                    const isItemNotInTheArray = inventoryItemsModified.find((item) => item?._id === inventoryItem?._id) == null;

                    if (isItemNotInTheArray) {
                        inventoryItemsModified.push(inventoryItem);
                    }
                }
            }

            const boxes = await getBoxesForInventoryItems(inventoryItemsModified);
            const lists = await getListsForInventoryItems(inventoryItemsModified);

            boxes.forEach((box) => {
                if (box != null) {
                    const inventoryItem = inventoryItemsModified.find((item) => {
                        if (item.box != null) {
                            return item.box.toString() === box._id.toString();
                        }
                        return false;
                    });

                    if (inventoryItem != null) {
                        const boxChange = `La nueva cantidad en la caja ${box.name} de ${card.name} son ${inventoryItem.quantity}`;
                        shopifyLog.changes.push(boxChange);
                    }
                }
            });

            lists.forEach((list) => {
                if (list != null) {
                    const inventoryItem = inventoryItemsModified.find((item) => {
                        if (item.list != null) {
                            return item.list.toString() === list._id.toString();
                        }
                        return false;
                    });

                    if (inventoryItem != null) {
                        const listChange = `La nueva cantidad en la carpeta ${list.name} de ${card.name} son ${inventoryItem.quantity}`;
                        shopifyLog.changes.push(listChange);
                    }
                }
            });

            await Promise.all(inventoryItemsModified.map(async (item) => await item?.save()));
        }
    }
}
```

## Database as Truth
### syncShopifyWithSetState
Function that updates Shopify with set data.
```tsx
import { Types, Document } from 'mongoose';
import { getSetsWithCards, getSet } from './SetsController';
import { getFullCardsForSet, getCardByID } from './CardsController';

export async function syncShopifyWithSetState(setID: Types.ObjectId, fromCronJob: boolean): Promise<void> {
    const cards = await getFullCardsForSet(setID);
    const set = await getSet(setID);

    for (const card of cards) {
        await syncShopifyWithCardState(card, set, null, fromCronJob);
    }
}
```

### syncShopifyWithListState
function that updates Shopify with the list (binder) data.
```tsx
import { Types, Document } from 'mongoose';
import { getList } from './ListsController';
import { getFullCardsForSet, getCardByID } from './CardsController';

export async function syncShopifyWithListState(listID: Types.ObjectId, fromCronJob: boolean): Promise<void> {
    const list = await getList(listID);

    if (list != null) {
        const cards = list.cards;

        for (const card of cards) {
            const cardData = await getCardByID(card, true);

            if (cardData != null) {
                await syncShopifyWithCardState(cardData, null, list, fromCronJob);
            }
        }
    }
}
```

### syncShopifyWithInventoryItemState
Function that updates Shopify with an **[InventoryItem](../models/InventoryItem)** data.
```tsx
import { getFullCardsForSet, getCardByID } from './CardsController';
import { getSetsWithCards, getSet } from './SetsController';
import { InventoryItemType } from '../servertypes/InventoryItemType';

export async function syncShopifyWithInventoryItemState(inventoryItem: InventoryItemType): Promise<void> {
    const card = await getCardByID(inventoryItem.card);

    if (card != null) {
        const set = await getSet(card.set);
        await syncShopifyWithCardState(card, set, null);
    }
}
```

### syncShopifyWithCardState
Function that receives a card and set, or a list, or a boolean as parameters, and updates card's data in Shopify, or creates a new producto in Shopify if parameters cannot be found.
```tsx
import { CardType } from '../servertypes/CardType';
import { SetType } from '../servertypes/SetType';
import { ListType } from '../servertypes/ListType';

async function syncShopifyWithCardState(card: CardType, set: SetType | null, list: ListType | null, fromCronJob?: boolean): Promise<void> {
    if (card.productID != null && card.productVariantID != null && card.inventoryLevelID != null && fromCronJob != null && !fromCronJob) {
        await Promise.all([
            updateCardProductVariantPriceInShopify(card),
            updateCardInventoryLevelInShopify(card),
            updateCardProductVariantSKUInShopify(card),
            waitForAPILimits()
        ]);
    } else if (
        card.productID != null &&
        card.productVariantID != null &&
        card.inventoryLevelID != null &&
        fromCronJob != null &&
        fromCronJob
    ) {
        await Promise.all([updateCardProductVariantPriceInShopify(card), waitForAPILimits()]);
    } else {
        if (set != null) {
            await Promise.all([createCardProductInShopify(card, set, list), waitForAPILimits()]);
        }
    }
}
```

### createCardProductInShopify
Function that receives a card, set and list as parameters, validates all data, then, gets InventoryItem from list (binder) or quantity from database and loads all of the data to Shopify's inventory. Finally, it gets Shopify's data and saves it to database for that product.
```tsx
import { CardType } from '../servertypes/CardType';
import { SetType } from '../servertypes/SetType';
import { ListType } from '../servertypes/ListType';
import Price from '../models/Price';

async function createCardProductInShopify(card: CardType, set: SetType, list: ListType | null): Promise<CardType> {
    const createProductsURL = SHOPIFY_URL + '/products.json';
    const lastPriceIndex = card.prices.length - 1;
    const isListDefined = list != null;
    const totalQuantity = isListDefined
        ? await getInventoryItemForCardInList(card._id, list._id)
        : await getInventoryMaxQuantityForCard(card._id);
    const latestPrice = await Price.findById(card.prices[lastPriceIndex]).exec();
    const cardSKU = card.sku != null ? card.sku : '';

    const requestBody = {
        product: {
            title: card.name + ': ' + (isListDefined ? list.name : set.name),
            body_html: card.body,
            vendor: 'El Tabernaculo',
            product_type: 'Card',
            handle: card.handle,
            tags: card.tags,
            images: [
                {
                    src: card.imageURL
                }
            ],
            variants: [
                {
                    option1: card.foil ? 'Foil' : 'Normal',
                    price: latestPrice != null ? latestPrice.value : 0,
                    requires_shipping: true,
                    taxable: false,
                    inventory_quantity: totalQuantity,
                    inventory_management: 'shopify',
                    inventory_policy: 'deny',
                    sku: cardSKU
                }
            ]
        }
    };

    const response = await fetchURL(createProductsURL, requestBody, 'POST');

    const shopifyJSON = await response.json();
    card.productID = shopifyJSON.product.id.toString();
    card.productVariantID = shopifyJSON.product.variants[0].id.toString();
    card.inventoryLevelID = shopifyJSON.product.variants[0].inventory_item_id.toString();
    await card.save();
    return card;
}

```

### updateCardProductVariantSKUInShopify
Function that updates variant SKU of a card in Shopify.
```tsx
import { CardType } from '../servertypes/CardType';

async function updateCardProductVariantSKUInShopify(card: CardType): Promise<CardType> {
    const updateVariantSkuURL = SHOPIFY_URL + `/variants/${card.productVariantID}.json`;

    const requestBody = {
        variant: {
            id: card.productVariantID,
            sku: card.sku
        }
    };

    await fetchURL(updateVariantSkuURL, requestBody, 'PUT');
    return card;
}
```

### updateCardProductVariantPriceInShopify
Function that updates variant price of a card in Shopify.
```tsx
import { CardType } from '../servertypes/CardType';
import Price from '../models/Price';

async function updateCardProductVariantPriceInShopify(card: CardType): Promise<CardType> {
    const updateVariantPriceURL = SHOPIFY_URL + `/variants/${card.productVariantID}.json`;
    const latestPrice = await Price.findById(card.prices[card.prices.length - 1]).exec();

    const requestBody = {
        variant: {
            id: card.productVariantID,
            price: latestPrice != null ? latestPrice.value : 0
        }
    };

    await fetchURL(updateVariantPriceURL, requestBody, 'PUT');

    return card;
}
```

### getCardInventoryLevelInShopify
Function that gets the specified card's level from Shopify's inventory. Level is the actual quantity of a product, for a particular location.
```tsx
import { SHOPIFY_URL, LOCATION_ID, SHOPIFY_WEBHOOK_KEY } from '../constants/constants';
import { CardType } from '../servertypes/CardType';

async function getCardInventoryLevelInShopify(card: CardType): Promise<any> {
    // TODO: It is possible to retrieve all inventory levels with just one query for all cards, we could optimize performance a lot by doing that.
    const getInventoryLevelURL =
        SHOPIFY_URL + '/inventory_levels.json?' + `inventory_item_ids=${card.inventoryLevelID}&` + `location_ids=${LOCATION_ID}`;

    const response = await fetchURL(getInventoryLevelURL, undefined, 'GET');

    const json = await response.json();
    const inventoryLevel = json.inventory_levels[0];
    return inventoryLevel.available;
}
```

### updateCardInventoryLevelInShopify
Function that updates a card's level in Shopify's inventory.
```tsx
import { SHOPIFY_URL, LOCATION_ID, SHOPIFY_WEBHOOK_KEY } from '../constants/constants';
import { CardType } from '../servertypes/CardType';

async function updateCardInventoryLevelInShopify(card: CardType): Promise<CardType> {
    const updateInventoryLevelURL = SHOPIFY_URL + '/inventory_levels/set.json';
    const totalQuantity = await getInventoryMaxQuantityForCard(card._id);
    const requestBody = {
        inventory_item_id: card.inventoryLevelID,
        location_id: LOCATION_ID,
        available: totalQuantity
    };
    await fetchURL(updateInventoryLevelURL, requestBody, 'POST');

    return card;
}
```

### waitForAPILimits
Function that stablish a delay between queries to Shopify.
```tsx
async function waitForAPILimits(): Promise<unknown> {
    return await new Promise((resolve) => {
        setTimeout(() => {
            resolve({});
        }, 300);
    });
}
```
 
### fetchURL
Function that try to fetch data on the specified URL up to 100 times, with or without a body, and a method, and returns a response. 
```tsx
async function fetchURL(url: string, body: Object | undefined, method: string): Promise<Response> {
    return await retry(
        async () => {
            const response = await fetch(url, {
                method,
                headers: getShopifyHeaders(),
                body: body != null ? JSON.stringify(body) : undefined
            });

            if (response.status === 429 || !response.ok) {
                // don't retry upon 429 or error
                throw Error('Too many requests');
            }

            return response;
        },
        { retries: 100 }
    );
}
```

### verifyWebhook
Function that validates if a webhook is valid.
```tsx
export function verifyWebhook(payload: any, hmac: string): boolean {
    const message = payload.toString();
    const genHash = crypto.createHmac('sha256', SHOPIFY_WEBHOOK_KEY).update(message, 'utf8').digest('base64');
    return genHash === hmac;
}
```

### updateCardInventory
Function that updates a cards' quantity from Shopify to database.
```tsx
import {
    getBoxesForInventoryItems,
    getListsForInventoryItems,
    updateInventoryItemQuantity,
    getInventoryMaxQuantityForCard,
    getInventoryItemForCardInList,
    getInventoryItemsForCard
} from './InventoryController';
import Card from '../models/Card';
import InventoryItem from '../models/InventoryItem';

export async function updateCardInventory(product: any): Promise<void> {
    const inventoryLevelIdOfCard = product.inventory_item_id;
    const newQuantityOfCard = product.available;

    const card = await Card.findOne({ inventoryLevelID: inventoryLevelIdOfCard }).exec();

    if (card != null) {
        const cardId = card._id;
        const inventoryItemOfCard = await InventoryItem.findOne({ card: cardId }).exec();

        if (inventoryItemOfCard != null) {
            const currentQuantityOfCard = inventoryItemOfCard.quantity;

            if (currentQuantityOfCard !== newQuantityOfCard) {
                const inventoryItemId = inventoryItemOfCard._id;
                await updateInventoryItemQuantity(inventoryItemId, newQuantityOfCard);
            }
        }
    }
}
