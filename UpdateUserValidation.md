## Update user validation

#### src/lib/validations/user.schema.ts
```bash
import { z } from "zod";


export const createUserSchema = z.object({
    name: z
    .string()
    .trim()
    .min(2, { message: "Name must be at least 2 characters" })
    .max(100, { message: "Name must not exceed 1000 characters" }),

    email: z
    .string()
    .trim()
    .toLowerCase()
    .email({ message: "Invalid email address" }),

    phone: z
    .string()
    .trim()
    .regex(/^01[3-9]\d{8}$/, { message: "Invalid Bangladesh phone number" })
    .optional(),

    address: z
    .string()
    .trim()
    .max(255)
    .optional(),

    city: z
    .string()
    .trim()
    .max(100)
    .optional(),
});

export const updateUserSchema = createUserSchema.partial();


export type CreateUserInput = z.infer<typeof createUserSchema>;
export type UpdateUserInput = z.infer<typeof updateUserSchema>;
```
---
