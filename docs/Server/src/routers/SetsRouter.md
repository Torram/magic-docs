Routes of methods for the Set Router.
```tsx
import { getSet, reloadAllSetsFromMTG, getAllSetNames } from '../controllers/SetsController';
import express from 'express';
import { isAValidToken } from '../controllers/TokenController';
import { errorCallback } from '../controllers/ErrorController';
import mongoose from 'mongoose';
const router = express.Router();

router.get('/', isAValidToken, async (req, res) => {
    try {
        const sets = await getAllSetNames();
        res.json(sets);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.get('/:id', isAValidToken, async (req, res) => {
    try {
        const id = new mongoose.Types.ObjectId(req.params.id);
        const set = await getSet(id);
        res.json(set);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.put('/', isAValidToken, async (req, res) => {
    try {
        const sets = await reloadAllSetsFromMTG();
        res.json(sets);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

module.exports = router;
```