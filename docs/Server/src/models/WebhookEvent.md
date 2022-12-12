Structure for a webhookEvent.

```tsx
import mongoose, { Schema } from 'mongoose';

export interface WebhookEventModelType {
    webhookID: string;
    eventTopic: string;
    executionDate: string;
}

const WebhookEventSchema = new Schema({
    webhookID: String,
    eventTopic: String,
    executionDate: String
});

const WebhookEvent = mongoose.model<WebhookEventModelType>('WebhookEvent', WebhookEventSchema);
export default WebhookEvent;
```