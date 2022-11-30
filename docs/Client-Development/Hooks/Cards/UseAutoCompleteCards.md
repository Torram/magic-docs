This custom hook creates a list for its use in an autocomplete field.

```ts
import { CardType } from '../../types/CardType';

export default function useAutoCompleteCards(cards: CardType[]): Array<{ value: string; label: string }> {
    if (cards != null) {
        return cards.map((card) => {
            return {
                value: card._id,
                label: card.name
            };
        });
    } else {
        return [];
    }
}
```