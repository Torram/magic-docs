Routes of methods for the InventoryLogs Router.
```tsx
import express from 'express';
import { getInventoryLogs } from '../controllers/InventoryLogController';
import { isAValidToken } from '../controllers/TokenController';
import { errorCallback } from '../controllers/ErrorController';
const router = express.Router();

router.get('/', isAValidToken, async (req, res) => {
    const { startDate, endDate } = req.query; // YYYY-MM-DD string date format

    try {
        if (startDate != null && endDate != null) {
            const inventoryLogs = await getInventoryLogs(startDate as string, endDate as string);
            res.json(inventoryLogs);
        }
    } catch (error: any) {
        errorCallback(error, res);
    }
});

module.exports = router;
```