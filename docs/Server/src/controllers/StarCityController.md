### getNameForCardSeparatedByHyphens
Helper function that formats a card's name to use it in an URL.
```tsx
function getNameForCardSeparatedByHyphens(cardName: string): string {
    const arrayString = cardName.split(' ');
    const newWords = [];

    for (const word of arrayString) {
        const newWord = replaceCharactersInString(word, false);
        newWords.push(newWord);
    }

    return newWords.join('-').replaceAll('--', '-');
}
```

### replaceCharactersInString
Function that formats a given string, removing symbols and specific characters for URL usage or general purpose.
```tsx
function replaceCharactersInString(string: string, isURL: boolean): string {
    let newString = string.replace('★', '').replace('†', '').replace(',', '').replace('\'', '').replace('"', '');
    if (!isURL) {
        newString = newString.replace('.', '').replace('//', '');
    }
    return newString;
}
```

### formatCardNumber
Function that formats a given card number, applying an also given modifier.
```tsx
function formatCardNumber(cardNumber: string, modifyNumber: boolean, leadingZeroes = true): string {
    const newCardNumber = leadingZeroes ? cardNumber.padStart(3, '0') : parseInt(cardNumber, 10).toString();
    return modifyNumber ? newCardNumber : cardNumber;
}
```

### formatURLForCard
This function receives a cards' data and uses it to create a valid formatted URL for the Star City Games website.
```tsx
function formatURLForCard(
    cardName: string,
    cardNumber: string,
    set: SetType,
    foil: boolean,
    variantNumber: string,
    modifyNumber: boolean,
    leadingZeroes: boolean,
    isUnderscore?: boolean
): string {
    const starCityGamesURL = 'https://starcitygames.com/';
    const newSetCode = set.code.length > 3 && set.code[0] === 'P' ? set.code.substring(1) : set.code;
    const lowerCaseSetCode = newSetCode.toLowerCase();
    const finish = foil ? 'enf' : 'enn';
    const finishUpper = `${finish.toUpperCase()}1`;
    const variantN = variantNumber === '1' ? '' : variantNumber;
    const underscore = isUnderscore != null && isUnderscore ? '_' : '-';
    let newCardNumber = cardNumber.replace('★', '').replace('s', '').replace('p', '');
    newCardNumber = formatCardNumber(cardNumber, modifyNumber, leadingZeroes);
    const url =
        set.code.length > 3 && set.code[0] === 'P' && cardNumber.includes('s')
            ? `${starCityGamesURL}${cardName}-sgl-mtg-prm-pre${underscore}${lowerCaseSetCode}${variantN}${underscore}${newCardNumber}-${finish}/?sku=SGL-MTG-PRM-PRE_${set.code}${variantN}_${newCardNumber}-${finishUpper}`
            : set.code.length > 3 && set.code[0] === 'P' && cardNumber.includes('p')
                ? `${starCityGamesURL}${cardName}-sgl-mtg-prm-pp${underscore}${lowerCaseSetCode}${variantN}${underscore}${newCardNumber}-${finish}/?sku=SGL-MTG-PRM-PP_${set.code}${variantN}_${newCardNumber}-${finishUpper}`
                : `${starCityGamesURL}${cardName}-sgl-mtg-${lowerCaseSetCode}${variantN}-${newCardNumber}-${finish}/?sku=SGL-MTG-${set.code}${variantN}-${newCardNumber}-${finishUpper}`;
    return url;
}
```

### getSearchURLForCard
Function that prepares a card's name for its use in _formatURLForCard_.
```tsx
function getSearchURLForCard(
    cardName: string,
    cardNumber: string,
    set: SetType,
    foil: boolean,
    variantNumber: string,
    modifyNumber: boolean,
    leadingZeroes: boolean,
    isUnderscore?: boolean
): string {
    const cardNameForURL = getNameForCardSeparatedByHyphens(cardName);
    return formatURLForCard(cardNameForURL, cardNumber, set, foil, variantNumber, modifyNumber, leadingZeroes, isUnderscore);
}
```

### getSoupForCard
Function that receives a card's data and formats it to a valid URL, then creates a _Soup_ object  for that URL.
```tsx
import { getVariantNameForCardFromSoup, createSoupForCard } from './SoupsController';

async function getSoupForCard(
    cardName: string,
    cardNumber: string,
    set: SetType,
    foil: boolean,
    variantNumber: string,
    formatCardNumber: boolean,
    leadingZeroes: boolean,
    isUnderscore?: boolean
): Promise<any> {
    let searchURL = '';

    if (cardNumber.includes('★')) {
        searchURL = getSearchURLForCard(cardName, cardNumber, set, true, variantNumber, formatCardNumber, leadingZeroes, isUnderscore);
    } else {
        searchURL = getSearchURLForCard(cardName, cardNumber, set, foil, variantNumber, formatCardNumber, leadingZeroes, isUnderscore);
    }
    searchURL = replaceCharactersInString(searchURL, true);
    return await createSoupForCard(searchURL);
}
```

## Prices
### getUpdatePriceForCard
Function that returns actual price value for a specific variant of a card from Star City Games.
```tsx
import Price, { PriceModelType } from '../models/Price';
import { Types, Document } from 'mongoose';
import { SetType } from '../servertypes/SetType';

export async function getUpdatedPriceForCard(
    cardName: string,
    cardNumber: string,
    set: SetType,
    foil: boolean
): Promise<(Document<unknown, any, PriceModelType> & PriceModelType & { _id: Types.ObjectId }) | undefined> {
    const cardVariants: CardVariants = { normal: '1', variant: '2', foiletched: '3' };
    for (const key in cardVariants) {
        const price = await getPriceForMTGCardInStarCity(cardName, cardNumber, set, foil, cardVariants[key]);
        if (price != null && price.value !== 0) {
            return price;
        }
    }
}
```

### getPriceWithFormattedCardNumber
Returns price of specific card, from a _Soup_ object of Star City Games Website or null if cannot find it.
```tsx
import Price, { PriceModelType } from '../models/Price';
import { Types, Document } from 'mongoose';
import { SetType } from '../servertypes/SetType';

async function getPriceWithFormattedCardNumber(
    cardName: string,
    cardNumber: string,
    set: SetType,
    foil: boolean,
    variantNumber: string,
    leadingZeroes: boolean,
    isUnderscore: boolean
): Promise<
    | (Document<unknown, any, PriceModelType> &
          PriceModelType & {
              _id: Types.ObjectId;
          })
    | null
> {
    try {
        const soup = await getSoupForCard(cardName, cardNumber, set, foil, variantNumber, true, leadingZeroes, isUnderscore);
        return await getPriceForCardInStarCity(soup);
    } catch (error) {
        return null;
    }
}
```

### getPriceForMTGCardInStarCity
This function returns a price value for a card, trying different variants of the card.
```tsx
import Price, { PriceModelType } from '../models/Price';
import { Types, Document } from 'mongoose';

export async function getPriceForMTGCardInStarCity(
    cardName: string,
    cardNumber: string,
    set: SetType,
    foil = false,
    variantNumber: string
): Promise<
    | (Document<unknown, any, PriceModelType> &
          PriceModelType & {
              _id: Types.ObjectId;
          })
    | null
> {
    try {
        const soup = await getSoupForCard(cardName, cardNumber, set, foil, variantNumber, false, false, false);
        const price = await getPriceForCardInStarCity(soup);

        if (price.value === 0) {
            let priceFormatted = await getPriceWithFormattedCardNumber(cardName, cardNumber, set, foil, variantNumber, false, false);
            if (price == null || price.value === 0) {
                priceFormatted = await getPriceWithFormattedCardNumber(cardName, cardNumber, set, foil, variantNumber, true, false);
            }
            return priceFormatted;
        }
        return price;
    } catch (error) {
        return null;
    }
}
```

### getPriceForCardInStarCity
This function receives a _Soup_ object and searches for a price value in it, then, returns a **_[Price](../models/Price)_** object.
```tsx
import Price, { PriceModelType } from '../models/Price';
import { Types, Document } from 'mongoose';

async function getPriceForCardInStarCity(
    soup: any
): Promise<Document<unknown, any, PriceModelType> & PriceModelType & { _id: Types.ObjectId }> {
    let value = 0;

    if (soup != null) {
        let dataSpan = soup.find('span', 'price--non-sale');

        let text = '';

        if (dataSpan != null) {
            text = dataSpan.text.replace('$', '').replace(',', '');
            value = parseFloat(text);
        }

        if (text == null || text === '') {
            dataSpan = soup.find('span', 'price--withoutTax');

            if (dataSpan != null) {
                text = dataSpan.text.replace('$', '').replace(',', '');
                value = parseFloat(text);
            }
        }
    }

    return await Price.create({
        value,
        creationDate: new Date()
    });
}
```
## VariantName

### getVariantNameWithFormattedCardNumber
Function that returns a call to _getVariantNameForCardFromSoup_ function, using a formatted card's data.
```tsx
import { SetType } from '../servertypes/SetType';
import { getVariantNameForCardFromSoup, createSoupForCard } from './SoupsController';

async function getVariantNameWithFormattedCardNumber(
    cardName: string,
    cardNumber: string,
    set: SetType,
    foil: boolean,
    variantNumber: string,
    leadingZeroes: boolean
): Promise<any> {
    try {
        const soup = await getSoupForCard(cardName, cardNumber, set, foil, variantNumber, true, leadingZeroes);
        return await getVariantNameForCardFromSoup(soup);
    } catch (error) {
        return null;
    }
}
```

### getVariantNameForMTGCard
Function that returns the formatted name of a card, from Star City Games website.
```tsx
import { getVariantNameForCardFromSoup, createSoupForCard } from './SoupsController';
import { SetType } from '../servertypes/SetType';

export async function getVariantNameForMTGCard(
    cardName: string,
    cardNumber: string,
    set: SetType,
    foil = false,
    variantNumber: string
): Promise<string | null> {
    try {
        const soup = await getSoupForCard(cardName, cardNumber, set, foil, variantNumber, false, false);
        let name = await getVariantNameForCardFromSoup(soup);

        if (name == null) {
            name = await getVariantNameWithFormattedCardNumber(cardName, cardNumber, set, foil, variantNumber, false);

            if (name == null) {
                name = await getVariantNameWithFormattedCardNumber(cardName, cardNumber, set, foil, variantNumber, true);
            }
        }

        return name;
    } catch (error) {
        return null;
    }
}


```