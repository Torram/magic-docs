Routes of methods for the Cards Router.
```tsx
import {
    getAllCards,
    updatePriceForCardByID,
    getCardsForList,
    updatePricesForCardsInList,
    getCardsForSet,
    orderCards,
    updatePricesForCardsInSet
} from '../controllers/CardsController';
import express from 'express';
import { isAValidToken } from '../controllers/TokenController';
import { errorCallback } from '../controllers/ErrorController';
// import { sendEmailForBoxes, sendEmailForFolders } from '../controllers/MailerController';
import mongoose from 'mongoose';
const router = express.Router();

router.get('/', isAValidToken, async (req, res) => {
    try {
        const cards = await getAllCards();
        res.json(cards);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.put('/:id', isAValidToken, async (req, res) => {
    try {
        const id = new mongoose.Types.ObjectId(req.params.id);
        const cards = await updatePriceForCardByID(id);
        res.json(cards);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

// List

router.get('/list/:id', isAValidToken, async (req, res) => {
    try {
        const id = new mongoose.Types.ObjectId(req.params.id);
        const cards = await getCardsForList(id);
        res.json(cards);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.put('/list/:id', isAValidToken, async (req, res) => {
    const id = new mongoose.Types.ObjectId(req.params.id);
    updatePricesForCardsInList(id)
        .then(() => {
            // sendEmailForFolders(req.params.id);
            console.log(`Updated prices for ${req.params.id}`);
        })
        .catch((error: any) => {
            errorCallback(error, res);
        });
    res.json({ success: true });
});

// Set

router.get('/set/:id', isAValidToken, async (req, res) => {
    try {
        const id = new mongoose.Types.ObjectId(req.params.id);
        const cards = await getCardsForSet(id);
        const orderedCards = await orderCards(cards);
        res.json(orderedCards);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.put('/set/:id', isAValidToken, async (req, res) => {
    const id = new mongoose.Types.ObjectId(req.params.id);
    updatePricesForCardsInSet(id)
        .then(() => {
            // sendEmailForBoxes(req.params.id);
            console.log(`Updated prices for ${req.params.id}`);
        })
        .catch((error: any) => {
            errorCallback(error, res);
        });
    res.json({ success: true });
});

// Cards should be created whenever you create a box for the first time, so this is mainly for testing purposes.
// router.post('/set/:id', isAValidToken, async (req, res) => {
//     try {
//         const set = await createCards(req.params.id);
//         const cards = await CardsController.getCardsForSet(set._id);
//         res.json(cards);
//     } catch (error) {
//         errorCallback(error, res);
//     }
// });

module.exports = router;
```