Main routing file, that packs all of the routes of the server.

```tsx
import express from 'express';
const BoxesRouter = require('./BoxesRouter');
const CardsRouter = require('./CardsRouter');
const SetsRouter = require('./SetsRouter');
const ListsRouter = require('./ListsRouter');
const InventoryRouter = require('./InventoryRouter');
const InventoryLogsRouter = require('./InventoryLogsRouter');
const ShopifyRouter = require('./ShopifyRouter');
const AuthRouter = require('./AuthRouter');

const router = express.Router();

router.use('/boxes', BoxesRouter);
router.use('/cards', CardsRouter);
router.use('/sets', SetsRouter);
router.use('/lists', ListsRouter);
router.use('/inventory_logs', InventoryLogsRouter);
router.use('/inventory', InventoryRouter);
router.use('/shopify', ShopifyRouter);
router.use('/auth', AuthRouter);

module.exports = router;
```