Routes of methods for the Lists Router.
```tsx
import express from 'express';
import {
    getAllLists,
    createList,
    getList,
    updateList,
    createOrderItem,
    updateOrderItem,
    updateOrderItems
} from '../controllers/ListsController';
import { isAValidToken } from '../controllers/TokenController';
import { errorCallback } from '../controllers/ErrorController';
import mongoose from 'mongoose';
const router = express.Router();

router.post('/', isAValidToken, async (req, res) => {
    const { name } = req.body;

    try {
        const list = await createList(name);
        res.json(list);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.get('/', isAValidToken, async (req, res) => {
    try {
        const lists = await getAllLists();
        res.json(lists);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.get('/:id', isAValidToken, async (req, res) => {
    try {
        const id = new mongoose.Types.ObjectId(req.params.id);
        const lists = await getList(id);
        res.json(lists);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.put('/:id', isAValidToken, async (req, res) => {
    const { cards } = req.body;

    try {
        const id = new mongoose.Types.ObjectId(req.params.id);
        const list = await updateList(id, cards);
        res.json(list);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.post('/:id/order_item/', isAValidToken, async (req, res) => {
    const { cardID, pageValue } = req.body;

    try {
        const id = new mongoose.Types.ObjectId(req.params.id);
        const list = await createOrderItem(id, cardID, pageValue);
        res.json(list);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.put('/:id/order_item/:orderItemId', isAValidToken, async (req, res) => {
    const { value } = req.body;

    try {
        const id = new mongoose.Types.ObjectId(req.params.id);
        const list = await updateOrderItem(id, value, id);
        res.json(list);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.put('/:id/order_items/', isAValidToken, async (req, res) => {
    const { orderItems } = req.body;

    try {
        const id = new mongoose.Types.ObjectId(req.params.id);
        const list = await updateOrderItems(orderItems, id);
        res.json(list);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

module.exports = router;
```