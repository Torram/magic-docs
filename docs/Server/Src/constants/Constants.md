Typescript file with all necessary constants for server functions and remote connections.

```tsx
const API_SECRET = 'CpVatkEtcJASKtbGBAS8jF4HHccrfJHC';
const API_SECRET_REFRESH = 'jTs0L1tVNMFmFl6LL4yr3J11Y7ghQask';
const ADMIN_KEY = 'EqLQbgygHAT83iyoZ9jfgd1eAOiLaBuG';

// Shopify
const LOCATION_ID = 12823003254;
const SHOPIFY_PASSWORD = '4cc4d7ca89b5fb611f3fa305936c90f6';
const SHOPIFY_URL =
    'https://' +
    'bb5f98deba98e80a3a0256c53117c67a' +
    ':' +
    SHOPIFY_PASSWORD +
    '@www-eltabernaculo-cards.myshopify.com/admin/api/' +
    '2022-01';
const SHOPIFY_WEBHOOK_KEY = '0715765360bca5dc75bf38947fe3c7056b17f5a6edc1090b699a27851522426d';

// TOKEN EXPIRATION
const TOKEN_EXP = 604800; // 7 days
const REFRESH_TOKEN_EXP = 2592000; // 30 days

export {
    SHOPIFY_URL,
    API_SECRET,
    API_SECRET_REFRESH,
    ADMIN_KEY,
    LOCATION_ID,
    SHOPIFY_PASSWORD,
    SHOPIFY_WEBHOOK_KEY,
    TOKEN_EXP,
    REFRESH_TOKEN_EXP
};
```