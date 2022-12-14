### errorCallBack
This function establishes the error structure for Responses of the server.
```tsx
import { Response } from 'express';

export function errorCallback(error: Error, res: Response): void {
    console.log(error.message);
    res.status(400);
    res.json({
        success: false,
        message: error.message
    });
}
```