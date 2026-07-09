## NextJS API---> Zod

#### Install
```bash
npm install zod
```
---

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
})

export type CreateUserInput = z.infer<typeof createUserSchema>;
```
---

#### app/api/user/create
```bash
import { NextRequest, NextResponse } from "next/server";
import prisma from "@/lib/prisma";
import { log } from "@/lib/logger";
import { createUserSchema } from "@/lib/validations/user.schema";

export async function POST(request: NextRequest) {
    try {

        log.info(
            {
                path: request.nextUrl.pathname,
                method: request.method,
            },
            "Create user received"
        );

        const jsonBody = await request.json();

        // Validation first -always
        const validation = createUserSchema.safeParse(jsonBody);

        if (!validation.success) {
            log.warn(
                { errors: validation.error.flatten() },
                "User creattion validation failed"
            );

            return NextResponse.json(
                {status: "error", message: "Invalid input", errors: validation.error.flatten().fieldErrors},
                {status: 400}
            )
        }

        const createUser = await prisma.user.create({
            data: validation.data,
        })

        log.info(
            {
                userId: createUser.id,
                email: createUser.email,
                createdAt: createUser.createdAt,
                updatedAt: createUser.updatedAt
            },
            "User created successfully!"
        );

        return NextResponse.json(
            {status: "success", message: "Your profile created!", data: createUser},
            {status: 201}
        )
    } catch (error) {
        log.error(
            { error, },
            "Fail to create profile"
        );
        return NextResponse.json(
            {status: "error", message: "Failed!, profile create"},
            {status: 500}
        )
    }
}
```
---
![](https://imgur.com/rXvNslT.png)
![](https://imgur.com/Acj3Tvf.png)
