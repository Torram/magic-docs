### HTMLEncode
Function that formats a given string to use it in an URL.
```tsx
function HTMLEncode(input: string): string {
    input = input.split(' ').join('+');
    return input;
}
```

### replaceAll
Function that replaces a string with another in a text.
```tsx
function replaceAll(string: string, search: string, replacement: string): string {
    return string.replace(new RegExp(search, 'g'), replacement);
}
```

### getNameForCardSeparatedByHyphens
Function that formats a card's name to use it in an URL.
```tsx
function getNameForCardSeparatedByHyphens(cardName: string): string {
    const arrayString = cardName.split(' ');
    const newWords: string[] = [];

    for (const word of arrayString) {
        const newWord = word.replace(',', '').replace('\'', '').replace('//', '');
        newWords.push(newWord);
    }

    return newWords.join('-').replace('--', '-');
}
```

### replaceSpecialCharactersOfCardNumber
Function that formats a card number, removing symbols.
```tsx
function replaceSpecialCharactersOfCardNumber(cardNumber: string): string {
    const newCardNumber = cardNumber.includes('★')
        ? cardNumber.replace('★', '%E2%98%85')
        : cardNumber.includes('†')
            ? cardNumber.replace('†', '%E2%80%A0')
            : cardNumber;
    return newCardNumber;
}
```

### getScryfallURLForCard
Function that creates a formatted URL of the given card.
```tsx
function getScryfallURLForCard(cardName: string, cardNumber: string, set: SetType): string {
    const cardNameForURL = getNameForCardSeparatedByHyphens(cardName);
    const lowerCaseSetCode = set.code.toLowerCase();
    const newCardNumber = replaceSpecialCharactersOfCardNumber(cardNumber);
    let url = `https://scryfall.com/card/${lowerCaseSetCode}/${newCardNumber}/${cardNameForURL}`;
    if (url.includes('&#39;')) url = url.replace('&#39;', '\'');
    const escapedURL = replaceSpecialCharacters(HTMLEncode(escape(url)));
    return replaceAll(escapedURL.toString(), '&amp;', '&');
}
```

### getPriceForMTGCardInScryfall
Function that creates a soup object and calls _getPriceForCardInScryfall_ to get price of a card in Scryfall.
```tsx
import { Types, Document } from 'mongoose';
import Price, { PriceModelType } from '../models/Price';

export async function getPriceForMTGCardInScryfall(
    cardName: string,
    cardNumber: string,
    set: SetType,
    foil: boolean
): Promise<Document<unknown, any, PriceModelType> & PriceModelType & { _id: Types.ObjectId }> {
    let url = getScryfallURLForCard(cardName, cardNumber, set);
    url = url.replace('★', '%E2%98%85').replace('†', '%E2%80%A0');
    const soup = await createSoupForCard(url);
    return await getPriceForCardInScryfall(soup, foil);
}
```

### getPriceForCardInScryfall
Function that receives a soup object, searches for price of a card on it and returns a **_[Price](../models/Price)_** object.
```tsx
import Price, { PriceModelType } from '../models/Price';
import { Types, Document } from 'mongoose';

async function getPriceForCardInScryfall(
    soup: any,
    foil: boolean
): Promise<Document<unknown, any, PriceModelType> & PriceModelType & { _id: Types.ObjectId }> {
    let dataSpanArray = [];
    let priceContent = null;
    let value = 0;

    if (foil && soup != null) {
        dataSpanArray = soup.findAll('span', 'currency-usd');

        if (dataSpanArray.length > 1) {
            priceContent = dataSpanArray[1];
        } else {
            priceContent = dataSpanArray[0];
        }
    } else if (soup != null) {
        priceContent = soup.find('span', 'currency-usd');
    }

    if (dataSpanArray.length === 0 && foil && priceContent.text == null) {
        dataSpanArray = soup.findAll('span', 'currency-usd-promo');

        if (dataSpanArray.length > 1) {
            priceContent = dataSpanArray[1];
        } else {
            priceContent = dataSpanArray[0];
        }
    } else if (soup != null && priceContent.text == null) {
        priceContent = soup.find('span', 'currency-usd-promo');
    }

    if (priceContent != null && priceContent.text !== '') {
        const text = priceContent.text
            .replace('$', '')
            .replace(',', '')
            .replace('✶', '');
        value = parseFloat(text);
    }

    return await Price.create({
        value,
        creationDate: new Date()
    });
}
```

### getImageForMTGCard
Function that calls to **[getImageCard](./SoupsController)** with generated soup object.
```tsx
import { SetType } from '../servertypes/SetType';

export async function getImageForMTGCard(cardName: string, cardNumber: string, set: SetType): Promise<any> {
    try {
        const url = getScryfallURLForCard(cardName, cardNumber, set);
        const soup = await createSoupForCard(url);
        return getImageForCard(soup);
    } catch (error) {
        return '';
    }
}
```
