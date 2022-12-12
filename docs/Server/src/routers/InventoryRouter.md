Routes of methods for the Inventory Router.
```tsx
import express from 'express';
import { getTokenFromRequest, isAValidToken } from '../controllers/TokenController';
import {
    createInventoryItem,
    updateInventoryItemQuantity,
    getInventoryItemsForCardsInBox,
    getInventoryItemsForCardsInList,
    getInventoryItemForCardInBox,
    getInventoryItemForCardInList
} from '../controllers/InventoryController';
import { createInventoryLog } from '../controllers/InventoryLogController';
import { errorCallback } from '../controllers/ErrorController';
import { syncShopifyWithInventoryItemState } from '../controllers/ShopifyController';
import { getBox } from '../controllers/BoxesController';
import { getCardsForSet, orderCards, orderCardsInList } from '../controllers/CardsController';
import jwt from 'jsonwebtoken';
import config from '../../config/config';
import mongoose from 'mongoose';
import { CardType } from '../servertypes/CardType';
const router = express.Router();

interface UserJwtPayload extends jwt.JwtPayload {
    id: string;
}

router.get('/box/:id', isAValidToken, async (req, res) => {
    const { cardID } = req.query;

    try {
        if (cardID != null) {
            const cardIdString = String(cardID);
            const inventoryItem = await getInventoryItemForCardInBox(cardIdString, req.params.id);
            res.json(inventoryItem);
        } else {
            const id = new mongoose.Types.ObjectId(req.params.id);
            const box = await getBox(id);

            if (box != null) {
                const boxSet = box.set._id.toString();
                const setId = new mongoose.Types.ObjectId(boxSet);
                const cards = await getCardsForSet(setId);
                const orderedCards = await orderCards(cards);
                const inventoryItems = await getInventoryItemsForCardsInBox(orderedCards, req.params.id);
                res.json(inventoryItems);
            }
        }
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.post('/box/:id', isAValidToken, async (req, res) => {
    const { cardID, value } = req.body;

    try {
        const id = new mongoose.Types.ObjectId(req.params.id);
        const inventoryItem = await createInventoryItem(cardID, id, null, value);

        if (JSON.stringify(config.NODE_ENV.trim()) === JSON.stringify('production')) {
            await syncShopifyWithInventoryItemState(inventoryItem);
            const token = await getTokenFromRequest(req);
            const decoded = jwt.decode(token) as UserJwtPayload;
            const decodedId = new mongoose.Types.ObjectId(decoded.id);
            await createInventoryLog(cardID, id, null, value, decodedId);
        }

        res.json(inventoryItem);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.get('/list/:id', isAValidToken, async (req, res) => {
    const { cardID } = req.query;

    try {
        if (cardID != null) {
            const cardId = new mongoose.Types.ObjectId(cardID as string);
            const listId = new mongoose.Types.ObjectId(req.params.id);
            const inventoryItem = await getInventoryItemForCardInList(cardId, listId);
            res.json(inventoryItem);
        } else {
            const id = new mongoose.Types.ObjectId(req.params.id);
            const cards = await orderCardsInList(id);
            const inventoryItems = await getInventoryItemsForCardsInList(cards as CardType[], id);
            res.json(inventoryItems);
        }
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.post('/list/:id', isAValidToken, async (req, res) => {
    const { cardID, value } = req.body;

    try {
        const id = new mongoose.Types.ObjectId(req.params.id);
        const inventoryItem = await createInventoryItem(cardID, null, id, value);

        if (JSON.stringify(config.NODE_ENV.trim()) === JSON.stringify('production')) {
            await syncShopifyWithInventoryItemState(inventoryItem);
            const token = await getTokenFromRequest(req);
            const decoded = jwt.decode(token) as UserJwtPayload;
            const decodedId = new mongoose.Types.ObjectId(decoded.id);
            await createInventoryLog(cardID, null, id, value, decodedId);
        }

        res.json(inventoryItem);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.put('/:id', isAValidToken, async (req, res) => {
    const { value } = req.body;

    try {
        const id = new mongoose.Types.ObjectId(req.params.id);
        const inventoryItem = await updateInventoryItemQuantity(id, value);

        if (JSON.stringify(config.NODE_ENV.trim()) === JSON.stringify('production') && inventoryItem != null) {
            await syncShopifyWithInventoryItemState(inventoryItem);
            const token = await getTokenFromRequest(req);
            const decoded = jwt.decode(token) as UserJwtPayload;
            const decodedId = new mongoose.Types.ObjectId(decoded.id);
            await createInventoryLog(inventoryItem.card, inventoryItem.box, inventoryItem.list, value, decodedId);
        }

        res.json(inventoryItem);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

module.exports = router;
```