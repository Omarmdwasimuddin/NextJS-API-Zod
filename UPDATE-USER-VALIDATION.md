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

#### app/api/user/update
```bash
import { NextRequest, NextResponse } from "next/server";
import prisma from "@/lib/prisma";
import { log } from "@/lib/logger";
import { updateUserSchema } from "@/lib/validations/user.schema";


export async function PUT(request: NextRequest) {
    try {

        log.info(
            {
                path: request.nextUrl.pathname,
                method: request.method,
            },
            "User update request received"
        );
        
        const {searchParams} = new URL(request.url);
        const id = searchParams.get('id');
        const jsonBody = await request.json();

        const validation = updateUserSchema.safeParse(jsonBody)

        if (!validation.success) {
            log.warn(
                {
                    errors: validation.error.flatten(),
                },
                "User update validation failed"
            );

            return NextResponse.json(
                {status: "error", message: "Invalid Input", errors: validation.error.flatten().fieldErrors},
                {status: 400}
            )
        }

        if (!id) {
            log.warn(
                {
                  id,  
                },
                "Missing user ID"
            );
            return NextResponse.json(
                {status: "Failed", message: "Missing 'id' query parameter"},
                {status: 400}
            )
        }

        const userUpdate = await prisma.user.update({
            where: { id },
            data: validation.data,
        })

        log.info(
            {
                userId: userUpdate.id,
                name: userUpdate.name,
                email: userUpdate.email,
                createdAt: userUpdate.createdAt,
                updatedAt: userUpdate.updatedAt,
            },
            "User updated successfully!"
        )

        return NextResponse.json(
            {status: "success", message: "User Update successfully!", data: userUpdate},
            {status: 200}
        )
    } catch (error) {
        log.error(
            { error, },
            "User update fail"
        )
        return NextResponse.json(
            {status: "Failed", message: "User Update Error"},
            {status: 500}
        )
    }
}
```
---
![](https://imgur.com/aEZ3uBF.png)
![](https://imgur.com/l8xhdzP.png)
