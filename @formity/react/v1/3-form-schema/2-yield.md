# Yield

Learn how the yield element is used in the schema.

## Usage

The **yield** element is used to yield values when navigating between steps. When values are yielded the `onYield` callback of the `Formity` component will be called.

To understand how it is used let's look at this example.

```tsx
import type { Schema, Form, Return, Yield } from "@formity/react";

import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

import {
  FormStep,
  FormStepContent,
  FormStepHeading,
  FormStepInputs,
  FormStepRow,
} from "./components/form-step";

import { Select } from "./components/input/select";
import { TextInput } from "./components/input/text-input";
import { NumberInput } from "./components/input/number-input";
import { NextButton } from "./components/buttons/next-button";
import { BackButton } from "./components/buttons/back-button";

import { MultiStep } from "./multi-step";

export type Values = [
  Form<{ name: string; surname: string; age: number }>,
  Yield<{
    next: [{ name: string; surname: string; age: number }];
    back: [];
  }>,
  Form<{ softwareDeveloper: string }>,
  Yield<{
    next: [{ softwareDeveloper: string }];
    back: [];
  }>,
  Return<{
    name: string;
    surname: string;
    age: number;
    softwareDeveloper: string;
  }>,
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
              <NextButton>Next</NextButton>
            </FormStepContent>
          </FormStep>
        </MultiStep>
      ),
    },
  },
  {
    yield: {
      next: ({ name, surname, age }) => [{ name, surname, age }],
      back: () => [],
    },
  },
  {
    form: {
      values: () => ({
        softwareDeveloper: ["yes", []],
      }),
      render: ({ values, onNext, onBack }) => (
        <MultiStep onNext={onNext} onBack={onBack}>
          <FormStep
            key="softwareDeveloper"
            defaultValues={values}
            resolver={zodResolver(
              z.object({
                softwareDeveloper: z.string(),
              }),
            )}
          >
            <FormStepContent>
              <FormStepHeading>Are you a software developer?</FormStepHeading>
              <FormStepInputs>
                <Select
                  name="softwareDeveloper"
                  label="Software developer"
                  options={[
                    { value: "yes", label: "Yes" },
                    { value: "no", label: "No" },
                  ]}
                />
              </FormStepInputs>
              <FormStepRow>
                <BackButton>Back</BackButton>
                <NextButton>Next</NextButton>
              </FormStepRow>
            </FormStepContent>
          </FormStep>
        </MultiStep>
      ),
    },
  },
  {
    yield: {
      next: ({ softwareDeveloper }) => [{ softwareDeveloper }],
      back: () => [],
    },
  },
  {
    return: ({ name, surname, age, softwareDeveloper }) => ({
      name,
      surname,
      age,
      softwareDeveloper,
    }),
  },
];
```

We need to use the `Yield` type and define the types of the values to be yielded when navigating to the next and previous steps.

```tsx
export type Values = [
  // ...
  Yield<{
    next: [{ name: string; surname: string; age: number }];
    back: [];
  }>,
  // ...
  Yield<{
    next: [{ softwareDeveloper: string }];
    back: [];
  }>,
  // ...
];
```

Then, in the schema we need to create an object with the following structure.

```tsx
export const schema: Schema<Values> = [
  // ...
  {
    yield: {
      next: ({ name, surname, age }) => [{ name, surname, age }],
      back: () => [],
    },
  },
  // ...
  {
    yield: {
      next: ({ softwareDeveloper }) => [{ softwareDeveloper }],
      back: () => [],
    },
  },
  // ...
];
```

The `next` function takes the input values and returns an array with the values to be yielded. Each array element will trigger the `onYield` callback function.

The `back` function works the same way with the difference that the values will be yielded when navigating to the previous steps.
