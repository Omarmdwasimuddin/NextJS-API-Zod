## Enum Validation

#### src/lib/validations/enum.schema.ts
```bash
import { z } from "zod";

const orderStatusEnum = z.enum(["PENDING", "PAID", "FAILED", "CANCELLED"])

export const orderStatusSchema = z.object({
    status: orderStatusEnum,
});

const transactionStatusEnum = z.enum(["INITIATED", "VALID", "FAILED", "CANCELLED", "EXPIRED"])

export const transactionStatusSchema = z.object({
    status: transactionStatusEnum,
});
```
#### Best Practice
```bash
import { z } from "zod";
import { OrderStatus, TransactionStatus } from "@/app/generated/prisma/client";


export const orderStatusSchema = z.object({
    status: z.nativeEnum(OrderStatus),
});

export const transactionStatusSchema = z.object({
    status: z.nativeEnum(TransactionStatus),
});
```
---
