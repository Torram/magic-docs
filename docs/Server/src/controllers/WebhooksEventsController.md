### createWebhookEvent
Function that creates a webhook event, of the **_[WebhookEvent](../models/WebhookEvent)_** type.
```tsx
import WebhookEvent, { WebhookEventModelType } from '../models/WebhookEvent';
import { Types, Document } from 'mongoose';

export async function createWebhookEvent(
    id: string,
    topic: string,
    date: string
): Promise<Document<unknown, any, WebhookEventModelType> & WebhookEventModelType & { _id: Types.ObjectId }> {
    return await WebhookEvent.create({
        webhookID: id,
        eventTopic: topic,
        executionDate: date
    });
}
```
### getWebhookEvent
Function that returns a webhook event if exists, with provided webhookID.
```tsx
import WebhookEvent, { WebhookEventModelType } from '../models/WebhookEvent';
import { Types, Document } from 'mongoose';

export async function getWebhookEvent(
    webhookId: string
): Promise<(Document<unknown, any, WebhookEventModelType> & WebhookEventModelType & { _id: Types.ObjectId }) | null> {
    return await WebhookEvent.findOne({ webhookID: webhookId }).exec();
}
```