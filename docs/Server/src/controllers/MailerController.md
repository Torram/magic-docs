### transporter
Object with authentication data for mail sending.
```tsx
import nodemailer from 'nodemailer';

const transporter = nodemailer.createTransport({
    service: 'gmail',
    auth: {
        user: 'contactoeltabernaculo@gmail.com',
        pass: 'FractalMagic1'
    }
});
```

### sendEmailForBoxes
Function that sends an email when a box is updated.
```tsx
import { Types } from 'mongoose';
import SMTPTransport from 'nodemailer/lib/smtp-transport';
import { getSet } from '../controllers/SetsController';

export async function sendEmailForBoxes(id: Types.ObjectId): Promise<SMTPTransport.SentMessageInfo | undefined> {
    const set = await getSet(id);

    if (set != null) {
        const mailOptions = {
            from: 'contactoeltabernaculo@gmail.com',
            to: 'rhoadsblast2016@gmail.com',
            subject: `Actualización en la caja ${set.name}`,
            text: `Se ha actualizado la caja ${set.name} exitosamente`
        };

        return await transporter.sendMail(mailOptions);
    }

    return;
}
```

### sendEmailForFolders
Function that sends an email when a list (binder) is updated.
```tsx
import { getList } from '../controllers/ListsController';
import { Types } from 'mongoose';
import SMTPTransport from 'nodemailer/lib/smtp-transport';

export async function sendEmailForFolders(id: Types.ObjectId): Promise<SMTPTransport.SentMessageInfo | undefined> {
    const list = await getList(id);

    if (list != null) {
        const mailOptions = {
            from: 'contactoeltabernaculo@gmail.com',
            to: 'rhoadsblast2016@gmail.com',
            subject: `Actualización en la carpeta ${list.name}`,
            text: `Se ha actualizado la carpeta ${list.name} exitosamente`
        };

        return await transporter.sendMail(mailOptions);
    }

    return;
}
```

### sendEmailForShopifyBoxes
Function that sends an email when a set is added to Shopify e-commerce.
```tsx
import { Types } from 'mongoose';
import SMTPTransport from 'nodemailer/lib/smtp-transport';

export async function sendEmailForShopifyBoxes(id: Types.ObjectId): Promise<SMTPTransport.SentMessageInfo | undefined> {
    const set = await getSet(id);

    if (set != null) {
        const mailOptions = {
            from: 'contactoeltabernaculo@gmail.com',
            to: 'rhoadsblast2016@gmail.com',
            subject: `Actualizacion de Shopify para el set ${set.name}`,
            text: `El set ${set.name} se ha subido a Shopify exitosamente`
        };

        return await transporter.sendMail(mailOptions);
    }

    return;
}
```

### sendEmailForShopifyFolder
Function that sends an email when a list (binder) is added to Shopify e-commerce.
```tsx
import { Types } from 'mongoose';
import SMTPTransport from 'nodemailer/lib/smtp-transport';

export async function sendEmailForShopifyFolder(id: Types.ObjectId): Promise<SMTPTransport.SentMessageInfo | undefined> {
    const list = await getList(id);

    if (list != null) {
        const mailOptions = {
            from: 'contactoeltabernaculo@gmail.com',
            to: 'rhoadsblast2016@gmail.com',
            subject: `Actualizacion de Shopify para la carpeta ${list?.name}`,
            text: `La carpeta ${list?.name} se ha subido a Shopify exitosamente`
        };

        return await transporter.sendMail(mailOptions);
    }

    return;
}
```