---
sidebar_position: 3
---
Main file of server. This file uses _Express JS_ framework as server, using the selected environment, and _Mongo DB_ as database through the mongoose library. This file also establishes cors configuration.

```tsx
import { Request, Response } from 'express';
import config from '../config/config';
const express = require('express');
const cors = require('cors');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const ApiRouter = require('../src/routers/ApiRouter');
const { getAllSets, reloadAllSetsFromMTG } = require('../src/controllers/SetsController');
const updateShopify = require('../src/jobs/updateShopify.job');

const whitelist = ['http://localhost:3000', 'https://magic-self.vercel.app', 'https://magic-production.vercel.app'];

// Express app creation and extensions.
const app = express();

app.use(
    cors({
        origin: function (origin: any, callback: any) {
            if (whitelist.indexOf(origin) !== -1 || origin == null) {
                callback(null, true);
            } else {
                callback(new Error('Not allowed by CORS'));
            }
        }
    })
);
app.options('*');

app.use(
    bodyParser.json({
        verify: (req: Request, res: Response, buf: any) => {
            req.rawHeaders = buf;
        }
    })
);
app.use(bodyParser.urlencoded({ extended: false }));
app.use(bodyParser.json());

// Connect to Mongo
//
mongoose
    .connect(config.DB_LINK)
    .then(async () => {
        // API calls
        app.use('/api', ApiRouter);

        const sets = await getAllSets();
        if (sets.length <= 0) {
            await reloadAllSetsFromMTG();
        }
        if (JSON.stringify(config.NODE_ENV.trim()) === JSON.stringify('production')) {
            updateShopify();
        }

        // Start server
        const port = process.env.PORT || 5001;
        const server = app.listen(port);
        server.timeout = 1000 * 60 * 60; // 10 minutes
        console.log(`Environment: ${config.NODE_ENV}`);
        console.log('Listening at port', port);
    })
    .catch((error: any) => {
        console.log(error.message);
    });
```