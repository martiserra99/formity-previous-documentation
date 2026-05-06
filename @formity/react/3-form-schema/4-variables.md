---
title: Variables
nextjs:
  metadata:
    title: Variables - Docs
    description: Learn how the variables element is used in the schema.
---

Learn how the variables element is used in the schema.

---

## Usage{% id="usage" %}

The **variables** element is used to define variables.

To understand how it is used let's look at this example.

```tsx
import type { Schema, Form, Return, Variables } from "@formity/react";

import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

import {
  FormStep,
  FormStepContent,
  FormStepHeading,
  FormStepInputs,
  FormStepRow,
} from "./components/form-step";

import { TextInput } from "./components/input/text-input";
import { NumberInput } from "./components/input/number-input";
import { NextButton } from "./components/buttons/next-button";

import { MultiStep } from "./multi-step";

export type Values = [
  Form<{ name: string; surname: string; age: number }>,
  Variables<{ nameSurname: string }>,
  Return<{ nameSurname: string }>,
];

export const schema: Schema<Values> = [
  {
    form: {
      values: () => ({
        name: ["", []],
        surname: ["", []],
        age: [20, []],
      }),
      render: ({ values, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="yourself"
            defaultValues={values}
            resolver={zodResolver(
              z.object({
                name: z
                  .string()
                  .min(1, { message: "Required" })
                  .max(20, { message: "Must be at most 20 characters" }),
                surname: z
                  .string()
                  .min(1, { message: "Required" })
                  .max(20, { message: "Must be at most 20 characters" }),
                age: z
                  .number()
                  .min(18, { message: "Minimum of 18 years old" })
                  .max(99, { message: "Maximum of 99 years old" }),
              }),
            )}
          >
            <FormStepContent>
              <FormStepHeading>Tell us about yourself</FormStepHeading>
              <FormStepInputs>
                <FormStepRow>
                  <TextInput name="name" label="Name" placeholder="Your name" />
                  <TextInput
                    name="surname"
                    label="Surname"
                    placeholder="Your surname"
                  />
                </FormStepRow>
                <NumberInput name="age" label="Age" placeholder="Your age" />
              </FormStepInputs>
              <NextButton>Submit</NextButton>
            </FormStepContent>
          </FormStep>
        </MultiStep>
      ),
    },
  },
  {
    variables: ({ name, surname }) => ({
      nameSurname: `${name} ${surname}`,
    }),
  },
  {
    return: ({ nameSurname, age }) => ({
      nameSurname,
      age,
    }),
  },
];
```

We need to use the `Variables` type and define the types of the variables.

```tsx
export type Values = [
  // ...
  Variables<{ nameSurname: string }>,
  // ...
];
```

Then, in the schema we need to create an object with the following structure.

```tsx
export const schema: Schema<Values> = [
  // ...
  {
    variables: ({ name, surname }) => ({
      nameSurname: `${name} ${surname}`,
    }),
  },
  // ...
];
```

The `variables` function takes the input values and returns the variables to be created.
