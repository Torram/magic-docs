Routes of methods for the Boxes Router.
```tsx
import express, { Request, Response } from 'express';
import { createBox, getBoxesForSet, getAllBoxes, getBox } from '../controllers/BoxesController';
import { isAValidToken } from '../controllers/TokenController';
import { errorCallback } from '../controllers/ErrorController';
import { createCards } from '../controllers/SetsController';
import mongoose from 'mongoose';
const router = express.Router();

router.post('/', isAValidToken, async (req: Request, res: Response) => {
    const { setID } = req.body;

    try {
        if (setID == null) throw Error('No setID provided');
        const setId = new mongoose.Types.ObjectId(setID);
        await createCards(setId);
        const box = await createBox(setId);
        res.json(box);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.get('/', isAValidToken, async (req: Request, res: Response) => {
    const { setID } = req.query;
    try {
        if (setID != null) {
            const setIdString = String(setID);
            const setId = new mongoose.Types.ObjectId(setIdString);
            const boxes = await getBoxesForSet(setId);
            res.json(boxes);
        } else {
            const boxes = await getAllBoxes();
            res.json(boxes);
        }
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.get('/:id', isAValidToken, async (req: Request, res: Response) => {
    try {
        const id = new mongoose.Types.ObjectId(req.params.id);
        const box = await getBox(id);
        res.json(box);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

module.exports = router;
```