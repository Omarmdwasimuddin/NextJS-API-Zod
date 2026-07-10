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

#### For import & use case 
```bash
import { env } from "@/lib/validations/env.schema";

formData.append("store_id", env.SSLCOMMERZ_STORE_ID);
formData.append("store_passwd", env.SSLCOMMERZ_STORE_PASSWORD);
```
#### `এখানে আর as string লাগবে না, কারণ Zod validate করার পর TypeScript জানে এগুলো string।`
---
