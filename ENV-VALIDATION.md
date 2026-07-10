## .env file validation

#### src/lib/validations/env.schema.ts
```bash
import { z } from "zod";


export const envSchema = z.object({

    DATABASE_URL: z.string().url(),
    DIRECT_URL: z.string().url().optional(),
    SSLCOMMERZ_STORE_ID: z.string().min(1),
    SSLCOMMERZ_STORE_PASSWORD: z.string().min(1),
    NODE_ENV: z.enum([ "development", "production", "test" ])

});

export const env = envSchema.parse(process.env);
```
---
