---
sidebar_position: 1
---
This file configures the environments through the _dotenv_ library.

```tsx
import dotenv from 'dotenv'; //variables de entorno
import path from 'path';
import { env } from 'process';

dotenv.config({
    path: path.resolve(__dirname, `../environments/${env.NODE_ENV?.trim()}.env`)
});

export default {
    NODE_ENV: process.env.NODE_ENV || 'development',
    DB_LINK: process.env.DB_LINK || 'mongodb+srv://admin:magic2021qa@cluster0.ewgkc.mongodb.net/MagicDBqa?retryWrites=true&w=majority'
};
```