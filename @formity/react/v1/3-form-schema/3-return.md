---
title: Return
nextjs:
  metadata:
    title: Return - Docs
    description: Learn how the return element is used in the schema.
---

Learn how the return element is used in the schema.

---

## Usage{% id="usage" %}

The **return** element is used to define what the multi-step form returns. This element will trigger the `onReturn` callback function of the `Formity` component.

To understand how it is used let's look at this example.

```tsx
import type { Schema, Form, Return } from "@formity/react";

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
  Return<{ nameSurname: string; age: number }>,
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
    return: ({ name, surname, age }) => ({
      nameSurname: `${name} ${surname}`,
      age,
    }),
  },
];
```

We need to use the `Return` type and define the types of the values to be returned.

```tsx
export type Values = [
  // ...
  Return<{ nameSurname: string; age: number }>,
];
```

Then, in the schema we need to create an object with the following structure.

```tsx
export const schema: Schema<Values> = [
  // ...
  {
    return: ({ name, surname, age }) => ({
      nameSurname: `${name} ${surname}`,
      age,
    }),
  },
];
```

The `return` function takes the input values and returns the values to be returned.
