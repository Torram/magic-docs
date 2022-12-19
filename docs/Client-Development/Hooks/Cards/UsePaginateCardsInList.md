This custom hook is used for generating the pagination of all of the **[OrderItems](../../Types)** in a list (binder). 

```tsx
import { useMemo } from 'react';
import { OrderItemType } from '../../types/OrderItemType';
import { CardType } from '../../types/CardType';

const getPageValue = (orderItem: OrderItemType, pageIndex: number): boolean => {
    const valueString = orderItem.value.toString();
    const valueStringArray = valueString.split('.');
    const pageValue = parseInt(valueStringArray[0]);
    return pageValue === pageIndex;
};

const getSortedCards = (items: OrderItemType[], cards: CardType[]): CardType[] => {
    return cards.filter((card) => {
        if (card != null) {
            const orderItemForCard = items.find((orderItem: OrderItemType) => orderItem.card === card._id.toString());
            return orderItemForCard != null && orderItemForCard.value !== 0;
        }
        return null;
    });
};

const getCardsForPage = (pageIndex: number, cards: CardType[], items: OrderItemType[]): CardType[] => {
    return cards.filter((card) => {
        if (card != null) {
            const orderItem = items.find((orderItem: OrderItemType) => orderItem.card === card._id.toString());
            if (orderItem != null) {
                return getPageValue(orderItem, pageIndex);
            }
        }
        return null;
    });
};

const orderCardsInPage = (items: OrderItemType[], pageCards: CardType[]): CardType[] => {
    return pageCards.sort((firstCard, secondCard) => {
        const orderItemOfFirstCard = items.find((orderItem: OrderItemType) => orderItem.card === firstCard._id.toString());
        const orderItemOfSecondCard = items.find((orderItem: OrderItemType) => orderItem.card === secondCard._id.toString());
        const valueOfFirstOrderItem = orderItemOfFirstCard?.value;
        const valueOfSecondOrderItem = orderItemOfSecondCard?.value;
        if (valueOfFirstOrderItem == null || valueOfSecondOrderItem == null) throw new Error('No item found');
        return valueOfFirstOrderItem - valueOfSecondOrderItem;
    });
};

const getLastPage = (items: OrderItemType[]): number => {
    const pagesArray = items.map((orderItem) => {
        const valueStringArray = orderItem.value.toString().split('.');
        const pageValue = parseInt(valueStringArray[0]);
        return pageValue;
    });
    return Math.max.apply(Math, pagesArray);
};

export default function usePaginateCardsInList(orderItems: OrderItemType[], cards: CardType[]): CardType[][] | undefined {
    return useMemo(() => {
        let totalPages = 0;
        let pagesArray: CardType[][] = [];
        let nonSortedCardsArray = [];

        if (orderItems != null && cards != null) {
            totalPages = getLastPage(orderItems);
            const sortedCards = getSortedCards(orderItems, cards);

            const nonSortedCards = cards.filter((card) => !sortedCards.includes(card));
            nonSortedCardsArray = [...nonSortedCards];

            for (let pageIndex = 1; pageIndex < totalPages + 1; pageIndex += 1) {
                const pageCards = getCardsForPage(pageIndex, cards, orderItems);
                const orderedPageCards = orderCardsInPage(orderItems, pageCards);
                const page = [...orderedPageCards];
                pagesArray = pagesArray.concat([page]);
            }

            pagesArray = pagesArray.filter((page) => page.length > 0);
            pagesArray = pagesArray.concat([nonSortedCardsArray]);
            return pagesArray;
        }
    }, [orderItems, cards]);
}
```