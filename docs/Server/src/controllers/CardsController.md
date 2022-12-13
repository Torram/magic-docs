## Get
### getCardsForList
Function that returns all of the cards for a _list_ (binder).
```tsx
import { Types, Document } from 'mongoose';
import Card, { CardModelType } from '../models/Card';
import List from '../models/List';

export async function getCardsForList(
    listID: Types.ObjectId
): Promise<Array<(Document<unknown, any, CardModelType> & CardModelType & { _id: Types.ObjectId }) | null> | undefined> {
    const list = await List.findById(listID).exec();

    if (list != null) {
        return await Promise.all(
            list.cards.map(async (cardID) => {
                return await Card.findById(cardID).populate({ path: 'prices' }).exec();
            })
        );
    }

    return;
}
```

### getCardById
Function that returns a query for get a specific card.
```tsx
import { Types, Document } from 'mongoose';
import Card, { CardModelType } from '../models/Card';

export async function getCardByID(
    cardID: Types.ObjectId,
    populate = false
): Promise<(Document<unknown, any, CardModelType> & CardModelType & { _id: Types.ObjectId }) | null> {
    if (populate) {
        const query = Card.findById(cardID).populate({ path: 'prices' });
        return await query.exec();
    }

    return await Card.findById(cardID).exec();
}
```

### getCardsForSet
Function that returns spccific values of all of the cards for a set.
```tsx
import Card, { CardModelType } from '../models/Card';
import { Types, Document } from 'mongoose';

export async function getCardsForSet(
    setID: Types.ObjectId
): Promise<Array<Omit<Document<unknown, any, CardModelType> & CardModelType & { _id: Types.ObjectId }, never>>> {
    const query = Card.find({ set: setID }, '_id name mtgName foil number price set imageURL sku').populate({ path: 'prices' });
    return await query.exec();
}
```

### getFullCardsForSet
Function that returns all values of all of the cards for a set.
```tsx
import Card, { CardModelType } from '../models/Card';
import { Types, Document } from 'mongoose';

export async function getFullCardsForSet(
    setID: Types.ObjectId
): Promise<Array<Omit<Document<unknown, any, CardModelType> & CardModelType & { _id: Types.ObjectId }, never>>> {
    const query = Card.find({ set: setID }).populate({ path: 'prices' });
    return await query.exec();
}
```

### getAllCards
Function that returns partial values of all of the cards in database.
```tsx
import Card, { CardModelType } from '../models/Card';
import { Types, Document } from 'mongoose';

export async function getAllCards(): Promise<
    Array<Omit<Document<unknown, any, CardModelType> & CardModelType & { _id: Types.ObjectId }, never>>
    > {
    const query = Card.find({}, '_id name price set').populate({ path: 'prices' });
    return await query.exec();
}
```

## Create
### createFoilCard
Function that gets data for a card from API, then creates a foil version of that card in database.
```tsx
import { ApiCardType } from '../servertypes/ApiCardType';
import { SetType } from '../servertypes/SetType';
import { Types, Document } from 'mongoose';
import Card, { CardModelType } from '../models/Card';

async function createFoilCard(
    mtgCard: ApiCardType,
    set: SetType
): Promise<Document<unknown, any, CardModelType> & CardModelType & { _id: Types.ObjectId }> {
    const sharedData = await createSharedDataForCard(mtgCard, set, true);
    sharedData.foil = true;
    sharedData.name = `${sharedData.name} - (Foil)`;
    return await Card.create(sharedData);
}
```

### createCard
Function that gets data for a card from API, then creates a card in database.
```tsx
import { ApiCardType } from '../servertypes/ApiCardType';
import { SetType } from '../servertypes/SetType';
import { Types, Document } from 'mongoose';
import Card, { CardModelType } from '../models/Card';

async function createCard(
    mtgCard: ApiCardType,
    set: SetType
): Promise<Document<unknown, any, CardModelType> & CardModelType & { _id: Types.ObjectId }> {
    const sharedData = await createSharedDataForCard(mtgCard, set, false);
    return await Card.create(sharedData);
}
```

### createCardsForSet
Function that creates an array of cards for a set.
```tsx
import { ApiCardType } from '../servertypes/ApiCardType';
import { Types, Document } from 'mongoose';
import Card, { CardModelType } from '../models/Card';
import { getMTGCards } from './MTGController';

export async function createCardsForSet(set: SetType): Promise<
    Array<
        | (Document<unknown, any, CardModelType> &
              CardModelType & { _id: Types.ObjectId; })
        | (Document<unknown, any, CardModelType> &
              CardModelType & { _id: Types.ObjectId; })
    >
> {
    let mtgCards: ApiCardType[] = [];
    const foilAndNormalFinishCards = [];
    const foilFinishOnlyCards = [];
    const normalFinishOnlyCards = [];

    const newCards = await getMTGCards(set.code);
    const cards = await filterCardsInArray(newCards);
    mtgCards = mtgCards.concat(cards);

    for (let i = 0; i < mtgCards.length; i += 1) {
        if (mtgCards[i].finishes.includes('nonfoil') && mtgCards[i].finishes.includes('foil')) {
            foilAndNormalFinishCards.push(mtgCards[i]);
        } else if (
            (mtgCards[i].finishes.includes('foil') && !mtgCards[i].finishes.includes('nonfoil')) ||
            (mtgCards[i].finishes.includes('etched') && !mtgCards[i].finishes.includes('nonfoil'))
        ) {
            foilFinishOnlyCards.push(mtgCards[i]);
        } else if (mtgCards[i].finishes.includes('nonfoil') && !mtgCards[i].finishes.includes('foil')) {
            normalFinishOnlyCards.push(mtgCards[i]);
        }
    }

    const normalOnlyCardsPromises = normalFinishOnlyCards.map(async (mtgCard) => {
        return await limitConcurrency?.(async () => await createCard(mtgCard, set));
    });

    const foilOnlyCardsPromises = foilFinishOnlyCards.map(async (mtgCard) => {
        return await limitConcurrency(async () => await createFoilCard(mtgCard, set));
    });

    const normalCardsPromises = foilAndNormalFinishCards.map(async (mtgCard) => {
        return await limitConcurrency(async () => await createCard(mtgCard, set));
    });

    const foilCardsPromises = foilAndNormalFinishCards.map(async (mtgCard) => {
        return await limitConcurrency?.(async () => await createFoilCard(mtgCard, set));
    });

    return await Promise.all([...normalOnlyCardsPromises, ...foilOnlyCardsPromises, ...normalCardsPromises, ...foilCardsPromises]);
}
```

### createSKUforCard
Function that creates the SKU for a given card, based on its variant, set and number.
```tsx
import { ApiCardType } from '../servertypes/ApiCardType';
import { SetType } from '../servertypes/SetType';

async function createSKUforCard(card: ApiCardType, set: SetType, foil: boolean, variantName: string): Promise<string> {
    const newCardNumber = card.number.length < 3 ? card.number.padStart(3, '0') : card.number;
    const isPromoSet = set.code.includes('P') && set.code.length > 3;
    const SKUletter =
        (isPromoSet && newCardNumber.includes('p') && foil) || (isPromoSet && variantName.includes('Promo Pack') && foil)
            ? 'E'
            : (isPromoSet && newCardNumber.includes('p') && !foil) || (isPromoSet && variantName.includes('Promo Pack') && !foil)
                ? 'D'
                : (isPromoSet && newCardNumber.includes('s') && foil) || (isPromoSet && variantName.includes('Prerelease') && foil)
                    ? 'F'
                    : card.name.includes('Buy-A-Box') || (isPromoSet && !newCardNumber.includes('s') && !newCardNumber.includes('p'))
                        ? 'C'
                        : !isPromoSet && foil
                            ? 'B'
                            : 'A';
    const sku = `${set.code}${newCardNumber}${SKUletter}`;
    return sku;
}
```
## Order
### orderCards
Function that order an array of cards by number.
```tsx
import { CardType } from '../servertypes/CardType'

export async function orderCards(cards: CardType[]): Promise<CardType[]> {
    cards.sort((a, b) => parseInt(a.number) - parseInt(b.number));
    return cards;
}
```

### orderCardsInList
Function that gets a listId and orders all cards in that _list_ (binder).
```tsx
import { Types, Document } from 'mongoose';
import List from '../models/List';
import { OrderItemType } from '../servertypes/OrderItemType';

export async function orderCardsInList(
    listID: Types.ObjectId
): Promise<Array<(Document<unknown, any, CardModelType> & CardModelType & { _id: Types.ObjectId }) | null> | undefined> {
    const list = await List.findById(listID);
    const cards = await getCardsForList(listID);

    if (list != null && cards != null) {
        const orderItems = list.orderItems;

        const nonZeroOrderItems = orderItems.filter((orderItem) => orderItem.value !== 0);
        const cardsWithOrderItems = cards.filter((card) => {
            return nonZeroOrderItems.some((orderItem) => {
                return card?._id.toString() === orderItem.card?.toString();
            });
        });
        const nonSortedCards = cards.filter((card) => !cardsWithOrderItems?.includes(card));

        const orderedCards = cardsWithOrderItems.sort((firstCard, secondCard) => {
            const orderItemOfFirstCard = nonZeroOrderItems.find(
                (orderItem: OrderItemType) => orderItem.card.toString() === firstCard?._id.toString()
            );
            const orderItemOfSecondCard = nonZeroOrderItems.find(
                (orderItem: OrderItemType) => orderItem.card.toString() === secondCard?._id.toString()
            );
            const valueOfFirstOrderItem = orderItemOfFirstCard?.value;
            const valueOfSecondOrderItem = orderItemOfSecondCard?.value;
            if (valueOfFirstOrderItem == null || valueOfSecondOrderItem == null) throw new Error('No item found');
            return valueOfFirstOrderItem - valueOfSecondOrderItem;
        });

        return orderedCards.concat(nonSortedCards);
    }

    return;
}
```

## Prices
### updatePriceForCard
Function that saves a new price for a card from SCG or Scryfall.
```tsx
import { CardType } from '../servertypes/CardType';
import { SetType } from '../servertypes/SetType';
import { Types, Document } from 'mongoose';
import Card, { CardModelType } from '../models/Card';

async function updatePriceForCard(
    card: CardType,
    set: SetType
): Promise<(Document<unknown, any, CardModelType> & CardModelType & { _id: Types.ObjectId }) | null> {
    let newPrice = await getUpdatedPriceForCard(card.mtgName ?? card.name, card.number, set, card.foil);

    if (newPrice == null || newPrice.value === 0) {
        newPrice = await getPriceForMTGCardInScryfall(card.mtgName ?? card.name, card.number, set, card.foil);

        if (newPrice != null) {
            card.prices.push(newPrice._id);
            await card.save();
        }
    } else {
        card.prices.push(newPrice._id);
        await card.save();
    }

    return await Card.findById(card._id).populate({ path: 'prices' }).exec();
}
```

### getPriceForCard
Function that returns a price of a card from SCG or Scryfall.
```tsx
import { Types, Document } from 'mongoose';
import { PriceModelType } from '../models/Price';

async function getPriceForCard(
    cardName: string,
    cardNumber: string,
    set: SetType,
    foil: boolean
): Promise<
    Document<unknown, any, PriceModelType> &
        PriceModelType & {
            _id: Types.ObjectId;
        }
> {
    const cardVariants: CardVariants = { normal: '1', variant: '2', foiletched: '3' };
    let price = null;

    for (const key in cardVariants) {
        price = await getPriceForMTGCardInStarCity(cardName, cardNumber, set, foil, cardVariants[key]);
        if (price != null && price.value !== 0) break;
    }

    if (price == null || price.value === 0) {
        price = await getPriceForMTGCardInScryfall(cardName, cardNumber, set, foil);
    }

    return price;
}
```

### updatePriceForCardByID
Function that updates price for specified card.
```tsx
import { Types, Document } from 'mongoose';
import Card, { CardModelType } from '../models/Card';

export async function updatePriceForCardByID(cardID: Types.ObjectId): Promise<
    | (Document<unknown, any, CardModelType> &
          CardModelType & {
              _id: Types.ObjectId;
          })
    | null
    | null
> {
    const card = await Card.findById(cardID).exec();

    if (card != null) {
        const set = await Set.findById(card.set).exec();

        if (set != null) {
            return await updatePriceForCard(card, set);
        }
    }

    return null;
}
```

### updatePricesForCardsInSet
This function updates prices for all cards in a set.
```tsx
import { Types, Document } from 'mongoose';
import Card, { CardModelType } from '../models/Card';

export async function updatePricesForCardsInSet(setID: Types.ObjectId): Promise<Array<
    | (Document<unknown, any, CardModelType> &
          CardModelType & {
              _id: Types.ObjectId;
          })
    | null
> | null> {
    const set = await Set.findById(setID).exec();
    const cards = await Card.find({ set: setID }).exec();

    if (set != null) {
        return await Promise.all(
            cards.map(async (card) => {
                return await limitConcurrency(async () => await updatePriceForCard(card, set));
            })
        );
    }

    return null;
}
```

### updatePricesForCardsInList
This function updates prices for all cards in a _list_ (binder).
```tsx
import { Types, Document } from 'mongoose';
import Card, { CardModelType } from '../models/Card';
import List from '../models/List';

export async function updatePricesForCardsInList(listID: Types.ObjectId): Promise<Array<
    | (Document<unknown, any, CardModelType> &
          CardModelType & {
              _id: Types.ObjectId;
          })
    | null
    | undefined
> | null> {
    const list = await List.findById(listID).exec();

    if (list != null) {
        return await Promise.all(
            list.cards.map(async (cardID) => {
                return await limitConcurrency(async () => await updatePriceForCardByID(cardID));
            })
        );
    }

    return null;
}
```

## Helpers
### interface CardVariants
Array of variants of a card.
```tsx
interface CardVariants {
    [index: string]: string;
}
```

### filterCardsInArray
Support function that removes repeated "dual cards" of an array.
```tsx
import { ApiCardType } from '../servertypes/ApiCardType';

async function filterCardsInArray(newCards: ApiCardType[]): Promise<ApiCardType[]> {
    let mtgCards: ApiCardType[] = [];
    let cardsNumbers: string[] = [];
    for (const card of newCards) {
        const checkIfNotRepeated = await findIfCardIsDualFacedAndItsAlreadyInSet(card, cardsNumbers);

        if (checkIfNotRepeated) {
            mtgCards = mtgCards.concat(card);
            cardsNumbers = cardsNumbers.concat(card.number);
        }
    }

    return mtgCards;
}
```

### findIfCardIsDualFacedAndItsAlreadyInSet
Helper function that checks if a card is dual faced and is in the array.
```tsx
import { ApiCardType } from '../servertypes/ApiCardType';

async function findIfCardIsDualFacedAndItsAlreadyInSet(card: ApiCardType, mtgCardsNumbers: string[]): Promise<boolean> {
    if (card.name.includes('//')) {
        const timesAppeared = mtgCardsNumbers.reduce(
            (previousValue, currentValue) => previousValue + Number(currentValue === card.number),
            0
        );
        if (timesAppeared === 1) return false;
    }

    return true;
}
```

### getVariantNameForCard
Function that returns variant names of a card if exists.
```tsx
import { SetType } from '../servertypes/SetType';

async function getVariantNameForCard(cardName: string, cardNumber: string, set: SetType, foil: boolean): Promise<string | undefined> {
    const cardVariants: CardVariants = { normal: '1', variant: '2', foiletched: '3' };

    for (const key in cardVariants) {
        const variantName = await getVariantNameForMTGCard(cardName, cardNumber, set, foil, cardVariants[key]);

        if (variantName !== 'English' && variantName != null) return variantName;
    }
}
```

### capitalizeFirstLetter
Helper function that change first letter of a string to capital.
```tsx
function capitalizeFirstLetter(string: string): string {
    return string.charAt(0).toUpperCase() + string.slice(1);
}
```

### getColorsForCard
Helper function that sets colors of a card.
```tsx
import { ApiCardType } from '../servertypes/ApiCardType';

function getColorsForCard(mtgCard: ApiCardType): string[] {
    const colors: string[] = [];

    if (mtgCard.colors != null && mtgCard.colors.length > 0) {
        mtgCard.colors.forEach((color: string) => {
            switch (color) {
            case 'W':
                colors.push('White');
                break;
            case 'B':
                colors.push('Black');
                break;
            case 'G':
                colors.push('Green');
                break;
            case 'R':
                colors.push('Red');
                break;
            case 'U':
                colors.push('Blue');
                break;
            }
        });
    }

    return colors;
}
```

### createSharedDataForCard
Helper function that sets general data for variants of the same card.
```tsx
import { ApiCardType } from '../servertypes/ApiCardType';
import { SetType } from '../servertypes/SetType';
import { Types, Document } from 'mongoose';

async function createSharedDataForCard(
    mtgCard: ApiCardType,
    set: SetType,
    foil: boolean
): Promise<{
    name: string;
    mtgName: string;
    prices: Types.ObjectId[];
    imageURL: any;
    tags: string;
    body: string;
    handle: string;
    set: Types.ObjectId;
    mtgID: string;
    number: string;
    types: string[];
    colors: string[];
    legalities: string[];
    cmc: string;
    foil: boolean;
    sku: string;
}> {
    const imageURL = await getImageForMTGCard(mtgCard.name, mtgCard.number, set);
    const price = await getPriceForCard(mtgCard.name, mtgCard.number, set, foil);
    const variantName = await getVariantNameForCard(mtgCard.name, mtgCard.number, set, foil);

    const newName = variantName != null ? `${mtgCard.name} ${variantName}` : mtgCard.name;
    const prices = price != null ? [price._id] : [];
    const sku = await createSKUforCard(mtgCard, set, foil, newName);
    const mtgCardColors = getColorsForCard(mtgCard);

    try {
        return {
            name: newName,
            mtgName: mtgCard.name,
            prices,
            imageURL,
            tags: getTagsForCard(mtgCard, set.name),
            body: getBodyForCard(mtgCard),
            handle: getHandleForCard(mtgCard, set.name),
            set: set._id,
            mtgID: mtgCard.identifiers.multiverseId,
            number: mtgCard.number,
            types: mtgCard.types,
            colors: mtgCardColors,
            legalities: getLegalitiesForCard(mtgCard),
            cmc: mtgCard.convertedManaCost,
            foil: false,
            sku
        };
    } catch (error: any) {
        throw Error(error.message);
    }
}
```

### getTagsForCard
Helper function that creates a string with all tags of a card.
```tsx
import { ApiCardType } from '../servertypes/ApiCardType';

function getTagsForCard(mtgCard: ApiCardType, setName: string): string {
    const mtgCardColors = getColorsForCard(mtgCard);
    const colors = mtgCardColors.length > 0 ? mtgCardColors.join(', ') : 'Sin Color';
    const types = mtgCard.types != null && mtgCard.types.length > 0 ? mtgCard.types.join(', ') : 'Sin tipos';
    const legalities = mtgCard.legalities != null ? getLegalitiesForCard(mtgCard).join(', ') : '';
    const rarity = capitalizeFirstLetter(mtgCard.rarity);
    return setName + ', ' + types + ', ' + colors + ', ' + rarity + ', ' + mtgCard.convertedManaCost + ', ' + legalities;
}
```
### getBodyForCard
Helper function that returns a _paragraph_ element with inner text of specified card.
```tsx
import { ApiCardType } from '../servertypes/ApiCardType';

function getBodyForCard(mtgCard: ApiCardType): string {
    if (mtgCard.text === '' || mtgCard.text === undefined) {
        return '<p></p>';
    } else {
        return '<p>' + mtgCard.text.replaceAll('\n', ' ') + '</p>';
    }
}
```

### getHandleForCard
Helper function that formats the name of a card to be properly used.
```tsx
import { ApiCardType } from '../servertypes/ApiCardType';

function getHandleForCard(mtgCard: ApiCardType, setName: string): string {
    return mtgCard.name.toLowerCase().replaceAll(' ', '-') + '-' + setName.toLowerCase().replaceAll(' ', '-');
}
```

### getLegalitiesForCard
Helper function that returns an array of all legalities applied to a card.
```tsx
import { ApiCardType } from '../servertypes/ApiCardType';

function getLegalitiesForCard(mtgCard: ApiCardType): string[] {
    if (mtgCard.legalities != null) {
        const legalitiesArrayKeys = Object.keys(mtgCard.legalities);
        const legalities: string[] = [];

        legalitiesArrayKeys.forEach((legality) => {
            const newLegalityString = capitalizeFirstLetter(legality);
            legalities.push(newLegalityString);
        });

        return legalities;
    }

    return [];
}
```
