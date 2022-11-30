---
sidebar_position: 1
---

This component creates a element that contains a changelog of the shopify inventory.

## Functions

### ShopifyLog

The function receives a log of the _ShopifyLogType_ and returns a element with the structured data of the log. 

```tsx
import { ShopifyLogType } from '../../types/ShopifyLogType';

export function ShopifyLog({ log }: { log: ShopifyLogType }): JSX.Element {
    return (
        <>
            <p>{`Fecha: ${new Date(log.creationDate).toLocaleDateString()}`}</p>
            <ul>
                {log.changes.map((change, index) => {
                    return <li key={index}>{change}</li>;
                })}
            </ul>
            <div className={'w-100 bg-dark'} style={{ height: '1px' }} />
        </>
    );
}
```