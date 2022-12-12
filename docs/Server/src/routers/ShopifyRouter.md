Routes of methods for the Shopify Router. 
```tsx
import express from 'express';
import { isAValidToken } from '../controllers/TokenController';
import { errorCallback } from '../controllers/ErrorController';
import {
    getAllShopifyLogs,
    syncDatabaseWithShopifyState,
    syncShopifyWithSetState,
    syncShopifyWithListState,
    verifyWebhook,
    updateCardInventory
} from '../controllers/ShopifyController';
import { createWebhookEvent, getWebhookEvent } from '../controllers/WebhooksEventsController';
// import { sendEmailForShopifyBoxes, sendEmailForShopifyFolder } from '../controllers/MailerController';
import config from '../../config/config';
import mongoose from 'mongoose';
const router = express.Router();

// We currently do this every time we change something in our database so this should only be used for testing or big differences to fix and should be used manually
// router.post('/sync_shopify_with_database', isAValidToken, async (req, res) => {
//     try {
//         await syncShopifyWithDatabaseState();
//         res.json({
//             success: true
//         });
//     } catch (error) {
//         errorCallback(error, res);
//     }
// });

router.post('/sync_database_with_shopify', isAValidToken, async (req, res) => {
    try {
        await syncDatabaseWithShopifyState();
        res.json({
            success: true
        });
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.post('/sync_shopify_with_list/:id', isAValidToken, async (req, res) => {
    if (JSON.stringify(config.NODE_ENV.trim()) === JSON.stringify('production')) {
        try {
            const id = new mongoose.Types.ObjectId(req.params.id);
            await syncShopifyWithListState(id, false).then(() => {
                /* We will uncomment this as soon as we change the mail server for the Nodemailer functions */
                // sendEmailForShopifyFolder(req.params.id);
                console.log('Shopify updated');
            });
            res.json({
                success: true
            });
        } catch (error: any) {
            errorCallback(error, res);
        }
    }
});

router.post('/sync_shopify_with_set/:id', isAValidToken, async (req, res) => {
    if (JSON.stringify(config.NODE_ENV.trim()) === JSON.stringify('production')) {
        try {
            const id = new mongoose.Types.ObjectId(req.params.id);
            await syncShopifyWithSetState(id, false).then(() => {
                /* We will uncomment this as soon as we change the mail server for the Nodemailer functions */
                // sendEmailForShopifyBoxes(req.params.id);
                console.log('Shopify updated');
            });
            res.json({
                success: true
            });
        } catch (error: any) {
            errorCallback(error, res);
        }
    }
});

router.get('/', isAValidToken, async (req, res) => {
    try {
        const shopifyLogs = await getAllShopifyLogs();
        res.json(shopifyLogs);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.post('/webhook_product_buyout', async (req, res) => {
    try {
        const hmac = req.header('X-Shopify-Hmac-Sha256');
        const webhookId = req.header('X-Shopify-Webhook-Id');
        const webhookTopic = req.header('X-Shopify-Topic');
        const data = req.body;
        const timeOffset = new Date().getTime() - new Date(req.body.updated_at).getTime();

        if (hmac != null && webhookId != null && webhookTopic != null) {
            const verified = verifyWebhook(data, hmac);

            if (!verified) {
                console.log('Error: No se pudo verificar el request');
                res.status(200).send('Could not verify request.');
                return;
            }

            const webhookAlreadySent = await getWebhookEvent(webhookId);

            if (webhookAlreadySent == null && timeOffset < 14000) {
                await createWebhookEvent(webhookId, webhookTopic, req.body.updated_at);

                await updateCardInventory(req.body).then(() => {
                    res.status(200).send('OK');
                });
            } else {
                res.status(200).send('Event duplicate.');
            }
        }
    } catch (error: any) {
        errorCallback(error, res);
    }
});

module.exports = router;
```