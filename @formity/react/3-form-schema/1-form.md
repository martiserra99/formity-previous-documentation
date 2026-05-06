---
title: Form
nextjs:
  metadata:
    title: Form - Docs
    description: Learn how the form element is used in the schema.
---

Learn how the form element is used in the schema.

---

## Usage{% id="usage" %}

The **form** element is used to define a form in the multi-step form.

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
  Return<{ name: string; surname: string; age: number }>,
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
      name,
      surname,
      age,
    }),
  },
];
```

We need to use the `Form` type and define the types of the values of the form.

```tsx
export type Values = [
  Form<{ name: string; surname: string; age: number }>,
  // ...
];
```

Then, in the schema we need to create an object with the following structure.

```tsx
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
  // ...
];
```

The `values` function defines the values of the form. It takes the input values and returns an object where each key maps to a tuple with the default value and an array.

When the user navigates between steps and returns to the same one, if the array remains unchanged, the default value will be the value previously entered.

```tsx
export const schema: Schema<Values> = [
  {
    form: {
      values: () => ({
        name: ["", []],
        surname: ["", []],
        age: [20, []],
      }),
      // ...
    },
  },
  // ...
];
```

The `render` function is used to render the form, and it takes as argument an object with the following properties:

- `inputs`: Input values.
- `values`: Default values of the form.
- `params`: Rendering-specific values injected from outside.
- `onNext`: Function to go to the next step. It takes the form values as argument.
- `onBack`: Function to go to the previous step. It takes the form values as argument.
- `getState`: Function to get the state. It takes the form values as argument.
- `setState`: Function to set the state. It takes the state as argument.

```tsx
export const schema: Schema<Values> = [
  {
    form: {
      // ...
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
  // ...
];
```

A `key` prop must be defined for each form to ensure the form's state updates correctly when navigating between steps.
