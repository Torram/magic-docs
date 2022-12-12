Routes of methods for the Auth Router. This functions uses jsonwebtoken library for session token handle.

```tsx
import express from 'express';
import { signUp, signIn, getUserByMail } from '../controllers/AuthController';
import jwt from 'jsonwebtoken';
import {
    generateToken,
    generateTokens,
    verifyRefreshToken,
    getTokenFromRequest,
    getRefreshTokenFromRequest,
    isAValidToken
} from '../controllers/TokenController';
import { errorCallback } from '../controllers/ErrorController';

const router = express.Router();

interface UserJwtPayload extends jwt.JwtPayload {
    username: string;
    email: string;
    id: string;
    type: string;
}

// AuthenticationRoutes

router.post('/sign_up', async (req, res) => {
    const { password, key, name, lastname, email } = req.body;

    const user = {
        name,
        lastname,
        email
    };

    try {
        const authData = await signUp(user, password, key);
        res.json(authData);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.post('/sign_in', async (req, res) => {
    const { password, email } = req.body;

    try {
        const authData = await signIn(email, password);
        res.json(authData);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

// Protected routes

router.get('/user', isAValidToken, async (req, res) => {
    try {
        const token = await getTokenFromRequest(req);
        const decoded = jwt.decode(token) as UserJwtPayload;
        const user = await getUserByMail(decoded.username);
        res.json(user);
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.get('/token', async (req, res) => {
    try {
        const token = await getTokenFromRequest(req);

        const refreshToken = await getRefreshTokenFromRequest(req);
        await verifyRefreshToken(refreshToken);

        const decoded = jwt.decode(token) as UserJwtPayload;

        res.json({
            token: generateToken(decoded.email, decoded.type, decoded.id)
        });
    } catch (error: any) {
        errorCallback(error, res);
    }
});

router.get('/refresh_token', async (req, res) => {
    try {
        const refreshToken = await getRefreshTokenFromRequest(req);
        await verifyRefreshToken(refreshToken);

        const decoded = jwt.decode(refreshToken) as UserJwtPayload;

        res.json(generateTokens(decoded.email, decoded.type, decoded.id));
    } catch (error: any) {
        errorCallback(error, res);
    }
});

module.exports = router;
```