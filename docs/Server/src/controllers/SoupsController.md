### replaceAll
Function used for strings replacement on a provided text.
```tsx
function replaceAll(string: string, search: string, replacement: string): string {
    return string.replace(new RegExp(search, 'g'), replacement);
}
```

### createSoupForCard
This function returns a JSSoup object of a card fetched data.
```tsx
const JSSoup = require('jssoup').default;
const fetchRetry = require('node-fetch-retry');

export async function createSoupForCard(url: string): Promise<any> {
    try {
        const response = await fetchRetry(url, {
            method: 'GET',
            retry: 100
        });

        const html = await response.text();
        return new JSSoup(html);
    } catch (error) {
        return;
    }
}
```

### getVariantNameForCardFromSoup
Function that searches a variant name of a card in the provided soup.
```tsx
export async function getVariantNameForCardFromSoup(soup: any): Promise<string | null> {
    const dataName = soup.find('span', 'productView-info-name');

    if (dataName != null) {
        return dataName.text.toString();
    }

    return null;
}
```

### getImageForCard
Function that searches for a card image url in a provided soup.
```tsx
export function getImageForCard(soup: any): any {
    const imageRow = soup.find('img', 'card');
    let result = imageRow.attrs.src;
    result = replaceAll(result, '&#58;', ':');
    result = replaceAll(result, '&#47;', '/');
    result = replaceAll(result, '&#63;', '?');
    result = replaceAll(result, '&#61;', '2');
    return result;
}
```