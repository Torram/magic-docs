Component that reports the changes of a card quantity, that was manually modified by an user.

```tsx
export function InventoryLog({ log }) {
    return (
        <>
            <p>{`Fecha: ${new Date(log.creationDate).toLocaleDateString()}`}</p>
            <p>{`${log.userName} modifico la ${log.boxName != null ? 'caja' : 'carpeta'}: ${log.boxName ?? log.listName} y la tarjeta: ${
                log.cardName
            }, la nueva cantidas es: ${log.quantity}`}</p>
            <div className={'w-100 bg-dark'} style={{ height: '1px' }} />
        </>
    );
}
```